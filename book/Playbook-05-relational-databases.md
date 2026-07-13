# Playbook 05 – Relational Databases

> Choose a relational database when relationships, transactions, and correctness matter more than schema flexibility.

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 25–30 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Interview Expectation** | Storage Design • Database Design • Scaling Decisions |
| **Difficulty** | Intermediate–Advanced |
| **Prerequisites** | Playbook 04 – Choosing the Right Storage |

---

## 📌 What Problem Does This Solve?

Many backend systems store structured, relational, transactional data. This playbook teaches **why** relational databases fit that data, how to model it, and how to scale it before jumping to more complex architectures.

---

## 🧠 Mental Model

```text
Understand the Data
        │
        ▼
Business Invariants
        │
        ▼
Relationships
        │
        ▼
Transactions
        │
        ▼
Schema Design
        │
        ▼
Indexes
        │
        ▼
Scaling Strategy
```

---

## What Makes a Database Relational?

A relational database stores structured data in tables connected by relationships.

It is most appropriate when:

- Relationships matter
- Transactions matter
- Strong consistency matters
- Rich querying matters

Examples:

- Users
- Orders
- Payments
- Inventory
- File metadata
- Permissions

---

## When Relational Storage Fits

| Requirement | Why It Matters |
|---|---|
| Stable schema | Predictable structure |
| Relationships | Tables reference one another |
| ACID transactions | Atomic updates |
| Strong consistency | Correct business state |
| Rich querying | Joins, filtering, aggregations |
| Data integrity | Constraints enforce correctness |

---

## ACID Transactions

| Property | Meaning |
|---|---|
| Atomicity | All operations succeed or none do |
| Consistency | Business rules remain valid |
| Isolation | Concurrent transactions behave correctly |
| Durability | Committed data survives failures |

Example:

Create Order → Reserve Inventory → Record Payment → Commit

The transaction protects the business invariant:

> Never sell inventory that does not exist.

---

## ⭐ Interview Insight

Don't simply say:

> "I need SQL."

Instead explain the invariant:

> "Orders and inventory must remain consistent so that two customers cannot purchase the final item."

---

## Schema Design

A schema should reflect:

- Data
- Relationships
- Query patterns
- Constraints

Example:

Users
 └──< Orders
        └──< OrderItems >── Products

Design the schema around **how the application reads and updates data**, not around individual screens.

---

## Normalization

Normalization removes duplicated data.

Example:

Poor:

Order
- customer_name
- customer_email

Better:

Customer
- customer_id
- name
- email

Order
- customer_id

### Benefits

- Better integrity
- Less duplication
- Easier updates

### Trade-off

More joins may be required.

---

## Denormalization

Denormalization intentionally duplicates data to improve reads.

Examples:

- Product name stored with an order
- Cached like counts
- Summary tables

Use when:

- Reads dominate
- Join cost is high
- Slight staleness is acceptable

Trade-off:

Update complexity increases.

---

## Indexes

Indexes create an additional searchable structure that speeds up reads.

Typical indexed columns:

- Foreign keys
- Frequently filtered columns
- Frequently sorted columns
- Join columns

Example:

SELECT * FROM orders
WHERE user_id = ?
ORDER BY created_at DESC;

A composite index on:

(user_id, created_at)

can significantly improve this query.

### Trade-offs

- Extra storage
- Slower inserts
- Slower updates
- Index maintenance

Design indexes around **real query patterns**, not every column.

---

## Read Replicas

Replicas improve read scalability.

Primary
 ├──► Replica
 └──► Replica

Common use cases:

- Product browsing
- User profiles
- Reporting

Avoid replicas for read-after-write operations unless replication lag is handled.

Trade-offs:

- Replication lag
- Operational complexity

---

## Scaling Writes

Writes are harder to scale because they update the source of truth.

### 1. Optimize Queries and Indexes

Improve inefficient SQL before changing architecture.

Examples:

- Add missing indexes
- Remove full table scans
- Eliminate N+1 queries

Trade-off:

Indexes improve reads but slow writes.

### 2. Batch Writes

Group multiple writes together.

Useful for:

- Analytics
- Logs
- Notifications

Trade-off:

Data becomes visible later.

### 3. Move Work Off the Request Path

Use queues and background workers.

Examples:

- Email
- Thumbnail generation
- Recommendations

Trade-off:

Eventual consistency.

### 4. Partition Tables

Split large tables.

Strategies:

- Range partitioning
- Hash partitioning

Choose partition keys that:

- Balance load
- Match query patterns
- Avoid hotspots

### 5. Shard the Database

Distribute data across multiple database servers.

Use only after simpler optimizations.

Common shard keys:

- user_id
- customer_id
- tenant_id
- region

Trade-offs:

- Cross-shard joins
- Cross-shard transactions
- Hot shards
- Rebalancing

> 💡 Rule of Thumb
>
> Better Queries
> ↓
> Better Indexes
> ↓
> Batch Writes
> ↓
> Async Processing
> ↓
> Partitioning
> ↓
> Sharding

Scale up before scaling out.

---

## Replication vs Partitioning vs Sharding

| Technique | Solves |
|---|---|
| Replication | Availability & read scaling |
| Partitioning | Organize very large tables |
| Sharding | Scale write throughput & storage |

---

## Failure Handling

Plan for:

- Primary failure
- Replica failure
- Network partition
- Disk failure
- Human error

Use:

- Replication
- Backups
- Point-in-time recovery

Remember:

Replication improves availability.

Backups protect against accidental deletion and corruption.

You usually need both.

---

## Worked Example – E-commerce

Requirements:

- Prevent overselling
- Preserve payment correctness
- Query customer orders

Storage:

- Orders → Relational DB
- Inventory → Relational DB
- Payments → Relational DB

Optimizations:

- Composite indexes
- Read replicas
- Cached catalog
- Async notifications

---

## 🎤 Interview Language

Instead of:

> "I'll shard."

Say:

> "I'd first verify writes are the bottleneck. If indexing, batching, asynchronous processing and partitioning are insufficient, I'd shard using a key aligned with the dominant access pattern."

---

## ✅ Interview Checklist

- Business invariants identified
- Transactions justified
- Schema reflects queries
- Indexes justified
- Replica usage explained
- Partitioning before sharding
- Backup strategy considered
- Trade-offs discussed

---

## ⚠️ Interview Traps

- Choosing SQL because data is structured
- Adding indexes everywhere
- Using replicas for write scaling
- Sharding too early
- Confusing replication with backups

---

## ☕ Backend Java Lens

Typical stack:

- Spring Data JPA / JDBC
- @Transactional
- Flyway / Liquibase
- HikariCP

Use idempotency keys and unique constraints for payments and order creation.

---

## 🎯 30-Second Recap

- Relational databases fit structured, transactional data.
- Transactions protect business invariants.
- Normalize for integrity.
- Denormalize selectively.
- Indexes optimize reads but slow writes.
- Read replicas scale reads.
- Scale writes progressively.
- Replication improves availability.
- Backups protect data.

---

## 💬 Practice Exercise

Design the relational storage layer for a ticket-booking system.

Discuss:

1. Tables
2. Relationships
3. Transactions
4. Indexes
5. Read replicas
6. Scaling
7. Trade-offs

---

## 💡 Key Takeaway

> Choose a relational database because relationships, transactions, constraints, and correctness matter—not simply because the data has columns.
