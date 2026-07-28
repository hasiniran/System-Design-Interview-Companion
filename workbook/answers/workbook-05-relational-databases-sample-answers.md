# Workbook 05 – Relational Databases: Sample Answers

> These are sample answers, not the only valid design. In a system design interview, explain the business invariant, justify the database mechanism, and state the trade-off.

---

## Exercise 1 – Business Invariants and Transactions

### 1.1 Identify the Invariants

| Operation | Business Invariant |
|---|---|
| Reserve a seat | At most one active, unexpired reservation may exist for a seat. |
| Confirm a booking | A booking may be confirmed only when the reservation is valid and payment has succeeded. A seat may belong to only one confirmed booking. |
| Process payment | Retrying the same logical payment request must not charge the customer more than once. |
| Cancel a booking | Cancelling an eligible booking must mark the booking as cancelled and release the seat exactly once. If payment was captured, a refund must be initiated. |
| Expire a reservation | Until `expires_at`, the seat remains unavailable. After expiration, the reservation must no longer block the seat. |

---

### 1.2 Define Transaction Boundaries

#### Reserve a Seat

```text
Steps that belong in the transaction:
1. Verify that the seat is currently available.
2. Atomically create an active reservation or change the seat state to HELD.
3. Store expires_at.
4. Commit.

Why:
The availability check and reservation creation must happen atomically so two users cannot reserve the same seat.
```

The system does not keep a transaction open for five minutes. It stores:

```text
expires_at = current_time + 5 minutes
```

A background worker or a later reservation attempt can detect and release expired reservations.

#### Confirm a Booking After Payment

```text
Steps that belong in the transaction:
1. Verify that the payment result has not already been processed.
2. Verify that the reservation is active and not expired.
3. Create the confirmed booking.
4. Mark the seat as BOOKED.
5. Mark the reservation as COMPLETED.
6. Commit.

Why:
The booking, seat, and reservation state must change together so the system cannot confirm a booking without securing the seat.
```

The external payment call should not remain inside a long-running database transaction. After commit, return the booking status and send confirmation asynchronously.

#### Cancel a Booking

```text
Steps that belong in the transaction:
1. Verify that the booking is eligible for cancellation.
2. Mark the booking as CANCELLED.
3. Release the seat.
4. Create a refund request or outbox event.
5. Commit.

Why:
The booking cancellation and seat release must remain consistent. The external refund can be processed asynchronously.
```

---

### 1.3 Concurrency Decision

```text
Decision:
Use an atomic conditional update, supported by a uniqueness constraint where possible.

Reason:
The database changes the seat from AVAILABLE to HELD only if it is still available.

Example:
UPDATE seats
SET status = 'HELD'
WHERE seat_id = ?
  AND status = 'AVAILABLE';

If zero rows are updated, another user already acquired the seat.

Trade-off:
Under heavy contention, many requests may fail and require retry handling. Expired holds must also be released reliably.
```

Alternative valid approaches include row-level locking, optimistic locking, partial unique indexes, or narrowly scoped serializable transactions.

---

## Exercise 2 – Schema and Index Design

### 2.1 Design the Core Tables

| Table | Primary Key | Important Columns | Foreign Keys |
|---|---|---|---|
| `users` | `user_id` | `first_name`, `last_name`, `email`, `phone` | None |
| `events` | `event_id` | `name`, `description`, `venue`, `start_time` | None |
| `seats` | `seat_id` | `seat_number`, `status`, `accessibility_type` | `event_id` |
| `reservations` | `reservation_id` | `status`, `expires_at`, `created_at` | `user_id`, `seat_id` |
| `bookings` | `booking_id` | `status`, `price`, `created_at` | `user_id`, `reservation_id`, optionally `seat_id` |
| `payments` | `payment_id` | `amount`, `status`, `idempotency_key`, `provider_payment_id` | `booking_id` |

Possible additional table:

| Table | Purpose |
|---|---|
| `outbox_events` | Stores durable events such as `BOOKING_CONFIRMED` and `REFUND_REQUESTED` in the same transaction as the related state change. |

Important notes:

- `users` does not have a foreign key to itself.
- `seats` should not store `user_id`; ownership belongs in reservations and bookings.
- `bookings` stores `user_id` because booking history is queried by user.
- `payments` stores an idempotency key rather than customer-name data.
- `reservations.event_id` may be omitted because the seat already identifies the event.

Useful additional rule:

```text
UNIQUE(event_id, seat_number)
```

---

### 2.2 Define Important Constraints

| Rule | Sample Constraint |
|---|---|
| A seat cannot have two active reservations | Partial unique index on `reservations(seat_id)` where `status = 'ACTIVE'`, or an atomic conditional update |
| A seat cannot belong to two confirmed bookings | Unique or partial unique constraint on `seat_id` for confirmed bookings |
| One payment request must not be processed twice | Unique constraint on `payments.idempotency_key` |
| A booking must belong to a valid user | Foreign key on `bookings.user_id` |
| A reservation must reference a valid event and seat | Foreign key on `reservations.seat_id`; add an event foreign key only if `event_id` is stored directly |

A row-level `CHECK` constraint cannot prevent two different rows from containing active reservations for the same seat.

Example check:

```sql
CHECK (expires_at > created_at)
```

Example partial unique index:

```sql
CREATE UNIQUE INDEX one_active_reservation_per_seat
ON reservations (seat_id)
WHERE status = 'ACTIVE';
```

---

### 2.3 Choose Indexes

#### Query A – Booking History

```text
Index:
(user_id, created_at DESC)

Why:
The database can find one user's bookings and return them in newest-first order without a separate full sort.

Write cost:
Every booking insert must update the composite index.
```

#### Query B – Available Seats

```text
Index:
(event_id, status)

Why:
The database narrows rows to one event and then filters by seat status.

Write cost:
Seat inserts and status changes must update the index.
```

Partial-index alternative:

```sql
CREATE INDEX available_seats_by_event
ON seats (event_id)
WHERE status = 'AVAILABLE';
```

#### Query C – Expired Reservations

```text
Index:
(status, expires_at)

Why:
The database filters by active status and then performs a range lookup on expires_at.

Write cost:
Reservation status and expiration updates require index maintenance.
```

Partial-index alternative:

```sql
CREATE INDEX active_reservations_by_expiration
ON reservations (expires_at)
WHERE status = 'ACTIVE';
```

#### Query D – Idempotent Payment Lookup

```text
Index or constraint:
UNIQUE(idempotency_key)

Why:
The unique constraint prevents duplicate logical payment requests and creates an index for fast lookup.
```

---

### 2.4 Index Judgment

1. **Why not index every column?**  
   Each index consumes storage and must be updated during inserts, updates, and deletes, increasing write cost and maintenance overhead.

2. **When is a composite index better than two separate indexes?**  
   When queries regularly filter, join, or sort using the same combination of columns.

3. **Why does column order matter?**  
   Composite B-tree indexes are generally most effective from the leftmost column onward, so the order should match the query's filtering and sorting pattern.

For example, `(user_id, created_at)` supports filtering by `user_id` and sorting by `created_at`, but usually does not efficiently support filtering only by `created_at`.

---

## Exercise 3 – Scaling the Relational Database

### Scenario 1 – Slow Booking History

```text
Bottleneck:
The booking-history query may be scanning too many rows or performing an expensive sort.

Next step:
Inspect the query plan, optimize the query, and add (user_id, created_at DESC).

Trade-off:
The index consumes storage and increases booking write cost.
```

### Scenario 2 – Heavy Event Browsing

```text
Bottleneck:
Read traffic is much higher than write traffic.

Next step:
Route suitable event and seat-detail reads to read replicas.

Trade-off:
Replication lag may return slightly stale results, and replica routing and failover add operational complexity.
```

The final reservation operation should still use the authoritative primary database.

### Scenario 3 – Expensive Analytics Writes

```text
Bottleneck:
The user request waits for a non-critical analytics write, and analytics traffic loads the primary database.

Next step:
Publish page-view events to a queue and process them asynchronously. Workers may batch writes or use a separate analytics store.

Trade-off:
Analytics becomes eventually consistent, and the system must handle retries, duplicates, queue failures, and backlog growth.
```

A queue smooths the workload but does not eliminate the underlying writes.

### Scenario 4 – Large Historical Table

```text
Bottleneck:
Queries and maintenance operate on a large table even though most operational traffic accesses recent records.

Next step:
Partition bookings by booking date.

Partition strategy:
Range partitioning by month or year using created_at.

Trade-off:
Queries without the partition key may scan multiple partitions. Cross-partition operations and maintenance become more complex.
```

### Scenario 5 – Hot Event

```text
Problem:
Most reservation writes and seat-state updates target one event, causing contention and concentrated load.

Why a poor shard key could make this worse:
If event_id is the shard key, every reservation for the popular event lands on one shard.

Better first step:
Optimize the reservation transaction, use atomic conditional updates, cache static event details, move non-critical work off the request path, and inspect lock contention before sharding.
```

```text
event_id
→ Easy event-level queries
→ Risk of a hot-event shard

hash(seat_id)
→ Better write distribution
→ Harder event-level queries
```

### Scenario 6 – Primary Write Limit

```text
Next step:
Shard the database horizontally.

Possible shard key:
customer_id for customer-centric booking data or hash(seat_id) for distributing reservation writes.

Why it matches the access pattern:
customer_id keeps a customer's booking history on one shard.
hash(seat_id) distributes reservation writes.

Main trade-off:
Cross-shard queries and transactions become harder, and routing, rebalancing, and hotspot management add operational complexity.
```

---

### 3.1 Put the Steps in Order

```text
1. Optimize inefficient queries
2. Add or correct indexes
3. Scale the database vertically
4. Batch compatible writes
5. Move non-critical work to a queue
6. Partition very large tables
7. Shard the database
```

This is a reasonable default progression, not a universal rule. Use the simplest technique that directly addresses the measured bottleneck.

---

### 3.2 Distinguish the Techniques

| Technique | Primary Goal | Does It Scale Writes? | Main Trade-off |
|---|---|---:|---|
| Read replica | Scale reads and improve availability | No | Replication lag and operational complexity |
| Table partitioning | Organize large tables and improve pruning and maintenance | Not by itself | Cross-partition queries and operational complexity |
| Database sharding | Scale storage and write throughput across database instances | Yes | Cross-shard queries, transactions, routing, and rebalancing |
| Backup | Recover from deletion, corruption, or disaster | No | Storage cost, backup management, and recovery time |
| Replication | Improve availability and optionally scale reads | No in a standard primary-replica design | Replication lag, failover complexity, and additional infrastructure |

#### Partitioning versus Sharding

```text
Partitioning
→ One logical database
→ Data divided into table partitions
→ Often still limited by the same database server

Sharding
→ Multiple database instances
→ Each instance owns part of the data
→ Adds aggregate write and storage capacity
```

#### Replication versus Backup

```text
Replication
→ Maintains live copies of current state
→ Helps availability and read scaling
→ May copy accidental deletion or corruption

Backup
→ Preserves recoverable historical state
→ Helps restore deleted or corrupted data
```

#### Vertical Scaling versus Replication

```text
Vertical scaling
→ Make one database server more powerful
→ Can improve the primary's capacity

Replication
→ Create additional copies of the database
→ Helps availability and read scaling
→ Does not increase primary write throughput in a standard primary-replica design
```

---

## Exercise 4 – Two-Minute Interview Response

### Why Relational?

Users, events, seats, reservations, bookings, and payments are structured and highly related. The system also requires transactions and strong consistency to prevent double booking and duplicate payments, so a relational database is an appropriate source of truth.

### Business Invariants

- A seat can have at most one active reservation.
- A seat can belong to at most one confirmed booking.
- An expired or cancelled reservation must no longer block the seat.
- The same payment request must not charge the customer more than once.
- A booking can be confirmed only after successful payment.

### Transaction Boundary

For reservation:

1. Verify the seat is available.
2. Atomically create an active reservation or hold the seat.
3. Store the expiration timestamp.
4. Commit.

For confirmation:

1. Verify the payment event is new.
2. Verify the reservation is active and unexpired.
3. Create the booking.
4. Mark the seat as booked.
5. Complete the reservation.
6. Commit.

Emails and page rendering happen outside the transaction.

### Constraints

- Partial unique constraint for one active reservation per seat
- Unique constraint for one confirmed booking per seat
- Unique constraint on `payments.idempotency_key`
- Foreign keys connecting the related records

### Indexes

```text
(user_id, created_at DESC)
```

supports user booking history.

```text
(event_id, status)
```

supports available-seat lookup.

### Read Scaling

Use query optimization, indexes, caching for mostly static event details, read replicas for stale-tolerant reads, and vertical scaling when the primary needs more capacity. The final reservation operation still uses the primary.

### Write Scaling

Progress through query optimization, appropriate indexes, vertical scaling, batching, queues for non-critical work, partitioning, and finally sharding when one database is no longer sufficient.

### Trade-offs

Indexes increase write cost, replicas introduce lag, caching creates invalidation problems, partitioning complicates cross-partition work, and sharding complicates transactions, joins, routing, and rebalancing.

---

## Rapid Recall – Sample Answers

1. **Why do read replicas not solve write scaling?**  
   Writes still go through the primary in a standard primary-replica architecture.

2. **Replication versus backup?**  
   Replication maintains live copies for availability and reads; backups preserve recoverable historical state.

3. **Why delay sharding?**  
   It adds distributed queries, transactions, routing, rebalancing, hotspot management, and operational complexity.

4. **What makes a good partition or shard key?**  
   High cardinality, even distribution of data and traffic, stability, hotspot avoidance, and alignment with access patterns.

5. **Main cost of indexes?**  
   Additional storage and write maintenance.

6. **Why can a queue help?**  
   It removes non-critical work from the request path, absorbs bursts, and enables asynchronous or batched processing.

7. **What does an idempotency key solve?**  
   It prevents retries of the same logical request from performing the operation more than once.

8. **What determines transaction boundaries?**  
   Business invariants and the database changes that must succeed or fail together.

---

## Final Reflection – Sample Answers

**Least automatic decision:** Choosing when sharding is necessary and selecting a shard key that balances distribution with query locality.

**Most important interview rule:** Verify the actual bottleneck and exhaust simpler options before partitioning or sharding.

---

## Key Takeaway

> Protect business invariants first. Then scale the measured bottleneck using the simplest technique that solves it.
