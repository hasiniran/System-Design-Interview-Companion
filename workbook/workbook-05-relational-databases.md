# Workbook 05 – Relational Databases

> Practice relational database decisions without repeating the full system-design process.

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 25–30 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Focus** | Transactions • Schema Design • Indexing • Scaling |
| **Prerequisites** | Playbook 05 – Relational Databases |
| **Recommended Format** | Write short answers, then explain them aloud |

---

## How to Use This Workbook

The system context is already provided.

Do **not** repeat:

- Functional requirements
- Non-functional requirements
- Capacity estimation
- Full high-level architecture

Focus only on the relational database decisions introduced in Playbook 05.

Use this reasoning pattern:

```text
Business Invariant
        ↓
Transaction Boundary
        ↓
Schema and Constraints
        ↓
Indexes
        ↓
Scaling Decision
        ↓
Trade-off
```

Keep most written answers to one or two sentences.

---

# Exercise 1 – Business Invariants and Transactions

## Scenario: Ticket Booking System

Users can:

- View an event
- View available seats
- Reserve a seat
- Pay for the reservation
- View booking history
- Cancel an eligible booking

Assume:

- A seat can belong to only one confirmed booking.
- A reservation expires after five minutes if payment is not completed.
- Payment processing is handled by an external provider.
- Users must not be charged twice for the same booking.

### 1.1 Identify the Invariants

| Operation | Business Invariant |
|---|---|
| Reserve a seat | |
| Confirm a booking | |
| Process payment | |
| Cancel a booking | |
| Expire a reservation | |

### 1.2 Define Transaction Boundaries

#### Reserve a Seat

```text
Steps that belong in the transaction:
1.
2.
3.

Why:
```

#### Confirm a Booking After Payment

```text
Steps that belong in the transaction:
1.
2.
3.

Why:
```

#### Cancel a Booking

```text
Steps that belong in the transaction:
1.
2.
3.

Why:
```

### 1.3 Concurrency Decision

Two users attempt to reserve the same seat at the same time.

Possible mechanisms:

- Unique constraint
- Row-level lock
- Optimistic locking
- Conditional update
- Serializable transaction

```text
Decision:

Reason:

Trade-off:
```

---

# Exercise 2 – Schema and Index Design

## Required Access Patterns

1. Find all seats for an event.
2. Find available seats for an event.
3. Find a user's bookings ordered by newest first.
4. Find a booking by booking ID.
5. Find an active reservation for a seat.
6. Find reservations that have expired.
7. Prevent duplicate payment processing.

## 2.1 Design the Core Tables

| Table | Primary Key | Important Columns | Foreign Keys |
|---|---|---|---|
| users | | | |
| events | | | |
| seats | | | |
| reservations | | | |
| bookings | | | |
| payments | | | |

You may add one additional table if needed.

## 2.2 Define Important Constraints

| Rule | Constraint |
|---|---|
| A seat cannot have two active reservations | |
| A seat cannot belong to two confirmed bookings | |
| One payment request must not be processed twice | |
| A booking must belong to a valid user | |
| A reservation must reference a valid event and seat | |

Possible mechanisms:

- Primary key
- Foreign key
- Unique constraint
- Check constraint
- Not-null constraint

## 2.3 Choose Indexes

### Query A

```sql
SELECT *
FROM bookings
WHERE user_id = ?
ORDER BY created_at DESC;
```

```text
Index:

Why:

Write cost:
```

### Query B

```sql
SELECT *
FROM seats
WHERE event_id = ?
  AND status = 'AVAILABLE';
```

```text
Index:

Why:

Write cost:
```

### Query C

```sql
SELECT *
FROM reservations
WHERE status = 'ACTIVE'
  AND expires_at < ?;
```

```text
Index:

Why:

Write cost:
```

### Query D

```sql
SELECT *
FROM payments
WHERE idempotency_key = ?;
```

```text
Index or constraint:

Why:
```

## 2.4 Index Judgment

1. Why should you not index every column?

```text
Answer:
```

2. When is a composite index better than two separate indexes?

```text
Answer:
```

3. Why does column order matter in a composite index?

```text
Answer:
```

---

# Exercise 3 – Scaling the Relational Database

For each scenario:

1. Identify the bottleneck.
2. Choose the **simplest reasonable next step**.
3. State one trade-off.

## Scenario 1 – Slow Booking History

Booking-history queries are slow, but writes are healthy.

```text
Bottleneck:

Next step:

Trade-off:
```

## Scenario 2 – Heavy Event Browsing

Event and seat details receive 20 times more reads than writes. Slightly stale data is acceptable.

```text
Bottleneck:

Next step:

Trade-off:
```

## Scenario 3 – Expensive Analytics Writes

Every page view is written synchronously to the primary database, increasing request latency.

```text
Bottleneck:

Next step:

Trade-off:
```

## Scenario 4 – Large Historical Table

The bookings table contains several years of data. Most operational queries access only the current year.

```text
Bottleneck:

Next step:

Partition strategy:

Trade-off:
```

## Scenario 5 – Hot Event

A single popular event receives most reservation traffic, while other events remain quiet.

```text
Problem:

Why a poor shard key could make this worse:

Better first step:
```

## Scenario 6 – Primary Write Limit

After query optimization, batching, asynchronous processing, and partitioning, one primary database can no longer handle the write volume.

```text
Next step:

Possible shard key:

Why it matches the access pattern:

Main trade-off:
```

## 3.1 Put the Steps in Order

Number these from the simplest intervention to the most complex.

```text
[ ] Add or correct indexes
[ ] Shard the database
[ ] Move non-critical work to a queue
[ ] Optimize inefficient queries
[ ] Partition very large tables
[ ] Batch compatible writes
[ ] Scale the database vertically
```

## 3.2 Distinguish the Techniques

| Technique | Primary Goal | Does It Scale Writes? | Main Trade-off |
|---|---|---:|---|
| Read replica | | | |
| Table partitioning | | | |
| Database sharding | | | |
| Backup | | | |
| Replication | | | |

---

# Exercise 4 – Two-Minute Interview Response

Use the ticket-booking system from Exercise 1.

Do not redesign the entire system.

Cover only the relational database layer:

1. Why a relational database is appropriate
2. The main business invariant
3. The core transaction boundary
4. The most important constraint
5. Two important indexes
6. How reads would scale
7. How writes would scale progressively
8. One important trade-off

```text
Why relational:

Business invariant:

Transaction boundary:

Constraint:

Indexes:

Read scaling:

Write scaling:

Trade-off:
```

---

# Rapid Recall – 5 Minutes

Answer each in one sentence.

1. Why do read replicas not solve write scaling?

```text
Answer:
```

2. What is the difference between replication and backup?

```text
Answer:
```

3. Why should sharding usually be delayed?

```text
Answer:
```

4. What makes a good partition or shard key?

```text
Answer:
```

5. What is the main cost of adding indexes?

```text
Answer:
```

6. Why might a queue help a write-heavy system?

```text
Answer:
```

7. What problem does an idempotency key solve?

```text
Answer:
```

8. What should determine transaction boundaries?

```text
Answer:
```

---

# Completion Check

You are ready to move to Playbook 06 when you can explain:

- Which business rule requires a transaction
- How the schema protects correctness
- Why each important index exists
- Why replicas scale reads but not writes
- Why partitioning and sharding are different
- Why sharding is not the first response to database load

You do not need perfect SQL syntax. The goal is sound architectural reasoning.

---

## Final Reflection

What relational database decision still feels least automatic?

```text
Answer:
```

What is the most important rule you want to remember in an interview?

```text
Answer:
```
