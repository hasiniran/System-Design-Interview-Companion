# Playbook 05 – Relational Databases

> Choose a relational database when relationships, transactions, and correctness matter more than schema flexibility.

---

## 📖 Study Card

|                           |                                          |
| ------------------------- | ---------------------------------------- |
| **Study Time**            | 15–20 minutes                            |
| **Priority**              | ⭐⭐⭐⭐⭐ Essential                          |
| **Interview Expectation** | Explain • Design • Discuss Trade-offs    |
| **Difficulty**            | Intermediate                             |
| **Prerequisites**         | Playbook 04 – Choosing the Right Storage |

---

## 📌 What Problem Does This Solve?

Many backend systems store data that is:

* Structured
* Related
* Transactional
* Frequently queried
* Required to remain correct

Examples include:

* Users
* Orders
* Payments
* Inventory
* Permissions
* File metadata

A relational database is often the safest starting point for this kind of data.

The interview challenge is not simply knowing that SQL exists.

It is knowing:

* Why relational storage fits the data
* Which guarantees matter
* How schema design affects queries
* How the database scales
* When relational storage stops being the best fit

---

## 🧠 Mental Model

```text
Structured Data
        │
        ▼
Relationships
        │
        ▼
Transactions
        │
        ▼
Relational Database
        │
        ▼
Indexes + Replication + Partitioning
```

Start with correctness.

Scale only when the workload requires it.

---

## What Makes a Database Relational?

A relational database stores data in tables.

Each table contains:

* Rows representing records
* Columns representing attributes
* Keys identifying records
* Relationships connecting tables

Example:

```text
Users
─────
user_id
name
email

Orders
──────
order_id
user_id
status
total
```

The `user_id` connects an order to its owner.

Relational databases are especially useful when those relationships are important to the business logic.

---

## When Relational Storage Fits

Choose relational storage when the system needs several of these:

| Requirement                      | Why It Matters                               |
| -------------------------------- | -------------------------------------------- |
| Stable or well-understood schema | Data fits predictable structures             |
| Strong relationships             | Tables can reference one another             |
| Transactions                     | Multiple changes succeed or fail together    |
| Strong consistency               | Reads reflect committed writes               |
| Rich querying                    | Filtering, joining, sorting, and aggregation |
| Data integrity                   | Constraints prevent invalid records          |

Typical examples:

* Banking
* E-commerce orders
* Payments
* Inventory
* User accounts
* Permissions
* Reservations

---

## ACID Transactions

ACID describes four transaction properties.

| Property        | Meaning                                              |
| --------------- | ---------------------------------------------------- |
| **Atomicity**   | All operations succeed or none do                    |
| **Consistency** | Data remains valid after the transaction             |
| **Isolation**   | Concurrent transactions do not interfere incorrectly |
| **Durability**  | Committed data survives failures                     |

### Example: Placing an Order

```text
Create Order
    │
    ▼
Reduce Inventory
    │
    ▼
Record Payment
    │
    ▼
Commit Transaction
```

If payment recording fails, the system may need to roll back the inventory update and order creation.

That is why transactions matter.

---

## ⭐ Interview Insight

Do not say:

> “I need strong consistency, so I’ll use SQL.”

That is incomplete.

Explain what must remain consistent.

For example:

> “Inventory and order creation must remain consistent so that two users cannot purchase the same final item. I would use transactional storage for the authoritative order and inventory records.”

The business invariant is more important than the database label.

---

## Schema Design

A good schema reflects:

* The data
* The relationships
* The dominant query patterns
* The required constraints

Example:

```text
Users
  │
  └──< Orders
          │
          └──< OrderItems >── Products
```

This supports queries such as:

* Find all orders for a user
* Find all items in an order
* Find the product referenced by an item

---

## Normalization

Normalization reduces duplicated data.

Example:

### Duplicated Design

```text
Order
─────
order_id
customer_name
customer_email
customer_address
```

The customer information may be repeated in every order.

### Normalized Design

```text
Customer
────────
customer_id
name
email

Order
─────
order_id
customer_id
```

### Benefits

* Less duplication
* Easier updates
* Better integrity

### Trade-off

Highly normalized schemas may require more joins.

---

## Denormalization

Denormalization intentionally duplicates selected data to improve read performance.

Examples:

* Store a product name in an order snapshot
* Store a precomputed like count
* Maintain a read-optimized summary table

Use it when:

* Reads dominate
* Joins are expensive
* Stale data is acceptable
* The duplicated value has clear update rules

> Denormalization trades storage and update complexity for faster reads.

---

## Indexes

An index speeds up reads by maintaining an additional searchable structure.

Example query:

```sql
SELECT *
FROM orders
WHERE user_id = ?;
```

An index on `user_id` can avoid scanning the entire orders table.

### Index When

* A column is frequently filtered
* A column is frequently joined
* Results are frequently sorted by it
* The query is important and selective

### Index Trade-offs

* Extra storage
* Slower inserts and updates
* More maintenance
* Too many indexes can hurt write performance

---

## Composite Indexes

A composite index contains multiple columns.

Example:

```text
(user_id, created_at)
```

This may help queries such as:

```sql
SELECT *
FROM orders
WHERE user_id = ?
ORDER BY created_at DESC;
```

Column order matters.

An index on:

```text
(user_id, created_at)
```

does not behave identically to:

```text
(created_at, user_id)
```

Design indexes around actual query patterns.

---

## Primary Keys

A primary key uniquely identifies a record.

Common choices:

### Auto-Incrementing ID

Advantages:

* Compact
* Efficient indexing
* Naturally ordered

Trade-offs:

* Harder to generate across independent database shards
* May reveal record volume

### UUID

Advantages:

* Can be generated independently
* Useful in distributed systems

Trade-offs:

* Larger keys
* Random UUIDs may reduce index locality

The correct choice depends on scale, distribution, and access patterns.

---

## Read Replicas

Read replicas copy data from the primary database.

```text
                ┌──► Read Replica
Primary DB ─────┼──► Read Replica
                └──► Read Replica
```

Use replicas when:

* Reads dominate writes
* Read traffic is overloading the primary
* Slight replication delay is acceptable

### Trade-offs

* Replication lag
* Stale reads
* More operational complexity
* Writes still go through the primary

---

## Scaling Writes

Read replicas do not solve write bottlenecks.

Options include:

* Optimize queries and indexes
* Batch writes
* Partition tables
* Shard data
* Separate unrelated workloads
* Move asynchronous work off the request path

Always identify the actual bottleneck first.

---

## Partitioning

Partitioning divides a large logical table into smaller pieces.

Common strategies:

### Range Partitioning

```text
Orders from Jan–Mar
Orders from Apr–Jun
Orders from Jul–Sep
```

Useful for:

* Time-based data
* Archival
* Range queries

Risk:

* Uneven traffic across ranges

### Hash Partitioning

```text
hash(user_id) % number_of_partitions
```

Useful for:

* Distributing data more evenly

Risk:

* Range queries become harder

---

## Sharding

Sharding distributes data across separate database instances.

```text
Users A–F ──► Shard 1
Users G–M ──► Shard 2
Users N–Z ──► Shard 3
```

Use sharding only when:

* A single database cannot handle the data or write load
* Vertical scaling is no longer sufficient
* The shard key is well understood

### Sharding Costs

* Cross-shard queries
* Cross-shard transactions
* Rebalancing
* Hot shards
* More difficult operations
* More complex application logic

> Sharding is powerful, but it is not a free optimization.

---

## Choosing a Shard Key

A good shard key should:

* Distribute data evenly
* Avoid hotspots
* Match common access patterns
* Minimize cross-shard queries
* Remain stable

Example:

```text
Shard by user_id
```

This works well when most requests concern one user’s data.

It works poorly when queries frequently aggregate across all users.

---

## Replication vs Partitioning

| Technique    | Primary Goal                           |
| ------------ | -------------------------------------- |
| Replication  | Availability and read scaling          |
| Partitioning | Organize or distribute data            |
| Sharding     | Scale data and writes across databases |

They solve different problems and are often used together.

---

## Strong Consistency vs Replication Lag

A read replica may not immediately contain the latest write.

Example:

```text
User updates profile
        │
        ▼
Primary commits update
        │
        ▼
Replica receives update later
```

If the next request reads from the replica, the user may briefly see old data.

Possible approaches:

* Read recent writes from the primary
* Use session-level read-after-write consistency
* Accept temporary staleness
* Route consistency-sensitive reads differently

Consistency requirements should be tied to the user experience.

---

## Failure Handling

Relational databases still fail.

Plan for:

* Primary database failure
* Replica failure
* Network partition
* Disk failure
* Corrupted deployment
* Accidental deletion

Common protections:

* Replication
* Automated failover
* Backups
* Point-in-time recovery
* Tested restoration procedures

> Replication improves availability. Backups protect against data loss and human error. You often need both.

---

## Worked Example – E-commerce Orders

### Data

* Orders
* Order items
* Customers
* Inventory
* Payments

### Requirements

* Prevent overselling
* Preserve payment correctness
* Track order state
* Query orders by customer
* Retain order history

### Storage Decision

Use relational storage for authoritative order, inventory, and payment records because:

* Relationships matter
* Transactions matter
* Constraints matter
* Strong consistency matters

### Possible Optimizations

* Index orders by customer and creation time
* Use read replicas for order history
* Cache product browsing data
* Process notifications asynchronously
* Partition historical orders by time when needed

### Trade-offs

* Write scaling is harder than with some distributed stores
* Replication introduces lag
* Sharding increases complexity

---

## 🎤 Interview Language

Instead of:

> “I’ll use a relational database.”

Say:

> “Orders, payments, and inventory are structured and transactional. I need constraints and atomic updates to preserve correctness, so I’ll use a relational database as the source of truth.”

Instead of:

> “I’ll add an index.”

Say:

> “Orders are frequently queried by customer and sorted by creation time, so I’d consider a composite index on customer ID and creation time. The trade-off is additional storage and slower writes.”

Instead of:

> “I’ll shard the database.”

Say:

> “I would first confirm that the primary bottleneck is write or storage capacity. If a single database can no longer handle the workload, I’d shard using a key aligned with the dominant access pattern, while acknowledging the cost of cross-shard queries and transactions.”

---

## ✅ Interview Checklist

Before finalizing a relational database design, ask:

* □ Which data needs transactions?
* □ What business invariants must remain correct?
* □ What are the dominant query patterns?
* □ Which columns need indexes?
* □ Which data can tolerate stale reads?
* □ Are reads or writes the bottleneck?
* □ Would read replicas help?
* □ Is partitioning sufficient before sharding?
* □ What is the backup and recovery plan?
* □ What trade-off does each optimization introduce?

---

## ⚠️ Interview Traps

* Choosing SQL only because the schema is structured
* Saying “strong consistency” without naming the invariant
* Adding indexes without explaining the query
* Assuming indexes are free
* Using read replicas to solve write bottlenecks
* Sharding too early
* Choosing a shard key that creates hotspots
* Treating replication as a backup
* Ignoring replication lag
* Normalizing or denormalizing without considering access patterns

---

## ☕ Backend Java Lens

A Spring Boot application commonly uses relational storage through:

| Concern            | Typical Approach                               |
| ------------------ | ---------------------------------------------- |
| Data access        | Spring Data JPA or JDBC                        |
| Transactions       | `@Transactional`                               |
| Schema migration   | Flyway or Liquibase                            |
| Connection pooling | HikariCP                                       |
| Query monitoring   | Database metrics and slow-query logs           |
| Resilience         | Timeouts, retries where safe, circuit breakers |

### Important Caution

Retries around database transactions can create duplicate side effects.

For operations such as payment or order creation, use:

* Idempotency keys
* Unique constraints
* Carefully bounded retries
* Explicit transaction boundaries

Framework annotations do not replace architectural reasoning.

---

## 📝 Whiteboard Sketch

```text
                 Application Services
                         │
                         ▼
                    Primary DB
                   /     |      \
                  /      |       \
                 ▼       ▼        ▼
          Read Replica  Backup  Read Replica
                 │
                 ▼
              Read Traffic
```

At greater scale:

```text
                 Application
                      │
            ┌─────────┴─────────┐
            ▼                   ▼
         Shard A             Shard B
      users 0–499K        users 500K–999K
```

---

## 🎯 30-Second Recap

* Relational databases fit structured, related, transactional data.
* ACID transactions protect business invariants.
* Normalize for integrity; denormalize selectively for read performance.
* Indexes improve reads but add storage and write cost.
* Read replicas scale reads, not writes.
* Partitioning and sharding solve different scaling problems.
* Sharding should come after simpler optimizations.
* Replication is not a substitute for backups.
* Every database decision should follow the access pattern and required guarantees.

---

## 💬 Practice Exercise

Design the relational storage layer for a ticket-booking system.

Cover:

1. Major tables and relationships
2. The invariant that prevents double booking
3. Transaction boundaries
4. Important indexes
5. Read-replica usage
6. The first scaling bottleneck
7. Whether and how you would partition or shard
8. At least three trade-offs

---

## 💡 Key Takeaway

> A relational database is not chosen merely because data has columns. It is chosen when relationships, transactions, constraints, and correctness are central to the system.
