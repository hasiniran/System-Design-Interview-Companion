# Playbook 06 – NoSQL Database Families

> Choose a NoSQL database when the access pattern, scale, latency, or data shape is a better fit than relational modeling.

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 25–30 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Interview Expectation** | Storage Selection • Data Modeling • Scaling Trade-offs |
| **Difficulty** | Intermediate |
| **Prerequisites** | Playbook 04 – Choosing the Right Storage • Playbook 05 – Relational Databases |

---

## 📌 What Problem Does This Solve?

A relational database is often the safest default for structured, transactional data.

However, some systems have requirements that relational storage does not handle as naturally:

- Extremely high write throughput
- Very low-latency key lookups
- Flexible or evolving document structure
- Massive datasets distributed across many nodes
- Relationship traversal as the dominant access pattern
- Access patterns that do not require joins or multi-row transactions

The interview challenge is not memorizing database products.

It is deciding:

1. Whether relational storage is still the best fit
2. Which NoSQL family matches the access pattern
3. Which guarantees are being relaxed
4. What new complexity the choice introduces

---

## 🧠 Mental Model

```text
Understand the Access Pattern
          │
          ▼
Required Guarantees
          │
          ▼
Data Shape
          │
          ▼
Scale and Latency
          │
          ▼
Choose the Database Family
          │
          ▼
Explain the Trade-off
```

A good database choice starts with:

> How will the application read and write this data?

Not:

> Which database is popular?

---

# 1. What Does NoSQL Mean?

NoSQL is a broad label for databases that do not use the traditional relational table-and-join model as their primary abstraction.

NoSQL does **not** mean:

- No schema
- No transactions
- No consistency
- No query language
- Automatically faster than SQL

Different NoSQL databases provide different guarantees.

The main families are:

1. Document
2. Key-value
3. Wide-column
4. Graph

Some technologies combine features from multiple families, so these categories are a reasoning tool rather than rigid boundaries.

---

# 2. When Relational Storage May Become a Poor Fit

Consider a NoSQL database when one or more of these conditions dominate.

## 2.1 The Access Pattern Is Simple and Repetitive

Example:

```text
Given session_id
        ↓
Return session data
```

A relational query engine may be unnecessary when every operation is a direct key lookup.

---

## 2.2 The Data Shape Varies Significantly

Example product attributes:

```text
Book
- author
- ISBN
- page_count

Laptop
- CPU
- memory
- storage

Shirt
- size
- color
- material
```

A document model may represent these variations naturally.

However:

> Flexible data does not automatically require a document database.

A relational database can still support optional columns, child tables, or JSON fields. The full workload must justify the choice.

---

## 2.3 Write Volume Is Extremely High

Examples:

- Telemetry
- Clickstream events
- IoT measurements
- Activity feeds
- Log ingestion

Some distributed NoSQL databases are designed to spread writes across many nodes using a partition key.

---

## 2.4 Horizontal Distribution Is a Primary Requirement

A system may need:

- Data distributed across many machines
- Regional placement
- Large aggregate storage capacity
- Continued operation during node failures

Some NoSQL systems make partitioning and replication part of the default architecture.

---

## 2.5 Relationship Traversal Is the Main Query

Example:

```text
User
  ↓ follows
User
  ↓ works_at
Company
  ↓ located_in
City
```

When multi-hop relationships are the core access pattern, a graph database may be more natural than repeated joins.

---

# 3. Access-Pattern-First Modeling

Relational modeling usually starts with entities and relationships:

```text
Users
Orders
Products
Payments
```

NoSQL modeling often starts with queries:

```text
Get profile by user_id
Get all orders for user_id
Get latest 20 messages for conversation_id
Get metrics for device_id within a time range
```

Then the data is shaped to make those specific operations efficient.

This may lead to duplication.

For example, order summaries may be stored directly under a user-oriented partition rather than reconstructed through joins.

---

## ⭐ Interview Insight

In a NoSQL design, say:

> "I would model the data around the dominant access patterns."

Then name those access patterns explicitly.

Do not say:

> "NoSQL is more scalable."

That statement is too broad.

A stronger explanation is:

> "The workload is dominated by high-volume writes partitioned by device ID, so I would choose a distributed store that can spread writes evenly across nodes."

---

# 4. Document Databases

A document database stores records as document-like structures, commonly resembling JSON.

Example:

```json
{
  "product_id": "p123",
  "name": "Trail Shoes",
  "category": "shoes",
  "attributes": {
    "size": [7, 8, 9, 10],
    "color": ["blue", "gray"],
    "waterproof": true
  }
}
```

---

## 4.1 Best Fit

Document databases fit when:

- Records are naturally retrieved as complete aggregates
- Fields vary between records
- Nested data belongs together
- Schema evolution is frequent
- Most queries stay within one document

Common examples:

- Product catalogs
- Content-management systems
- User preferences
- Form submissions
- Configuration documents

---

## 4.2 Aggregate-Oriented Modeling

A document should usually contain data that:

- Is read together
- Is updated together
- Has the same lifecycle
- Does not grow without bound

Example:

```text
User Profile Document
- basic profile
- preferences
- notification settings
```

This may be better than splitting these values across many tables when they are almost always retrieved together.

---

## 4.3 Embedding versus Referencing

### Embed

Store related data inside the document.

```text
Order
 ├── shipping_address
 └── order_items
```

Use embedding when:

- The child belongs to one parent
- Data is read together
- The child does not grow without limit

### Reference

Store an identifier pointing to another document.

```text
Order
 └── customer_id
```

Use references when:

- Data is shared
- Data changes independently
- Duplication would be expensive
- The relationship is many-to-many

---

## 4.4 Trade-offs

- Cross-document joins may be limited or expensive
- Data duplication is common
- Multi-document transactions may be more complex
- Large documents may become expensive to update
- Unbounded arrays can cause document growth problems
- Flexible schemas can lead to inconsistent data without validation

---

## Interview Language

> "Product attributes vary significantly by category, and each product is usually retrieved as one aggregate. A document model fits that access pattern, but I would avoid embedding unbounded data such as all reviews inside the product document."

---

# 5. Key-Value Stores

A key-value store maps a unique key to a value.

```text
Key                  Value

session:abc123   →   {user_id: 42, expires_at: ...}

feature:user:42  →   {new_checkout: true}
```

The core operation is:

```text
GET(key)
PUT(key, value)
DELETE(key)
```

---

## 5.1 Best Fit

Key-value stores fit when:

- Every lookup starts with a known key
- Very low latency matters
- Query flexibility is limited
- Values can be treated as opaque objects
- Independent entries can be partitioned easily

Common examples:

- Sessions
- Shopping carts
- Feature flags
- Rate-limit counters
- Idempotency records
- Simple preferences
- Distributed locks, with careful semantics

---

## 5.2 Why They Scale Well

The key can often be hashed to determine the owning partition:

```text
hash(key)
   │
   ├── Partition 1
   ├── Partition 2
   └── Partition 3
```

Independent keys are easy to distribute across nodes.

A good key should:

- Be stable
- Have high cardinality
- Distribute traffic evenly
- Avoid concentrating hot users or tenants

---

## 5.3 Trade-offs

- Limited filtering and ad hoc queries
- Relationships are handled in application code
- Secondary indexes may be limited
- Multi-key transactions may be restricted
- Hot keys can overload one partition
- Designing the key format becomes an important architectural decision

---

## Interview Language

> "Session access is always by session ID, and sessions have a natural expiration time. A key-value store provides direct low-latency lookup, but it would not support rich querying across session attributes."

---

# 6. Wide-Column Databases

A wide-column database stores rows identified by a partition key, with columns organized for large-scale distributed access.

A simplified model:

```text
Partition Key: user_id

Rows ordered by: event_time

user_42
  ├── 2026-07-27T10:00 event_A
  ├── 2026-07-27T10:03 event_B
  └── 2026-07-27T10:05 event_C
```

The exact implementation differs by technology, but the central idea is:

> Data is organized around a partition key and predictable query pattern.

---

## 6.1 Best Fit

Wide-column databases fit when:

- Write throughput is extremely high
- Data is distributed across many nodes
- Queries are known in advance
- Access is usually by partition key
- Range scans within a partition are common
- Joins are not required

Common examples:

- Activity feeds
- IoT events
- Messaging history
- Clickstreams
- Large-scale counters
- Time-ordered user events

---

## 6.2 Partition Key and Clustering Order

A typical design uses:

- **Partition key** to choose the node
- **Clustering key** to order records within the partition

Example:

```text
Partition key: conversation_id
Clustering key: message_timestamp
```

This efficiently supports:

```text
Get the latest messages for one conversation
```

---

## 6.3 Query-Driven Tables

A wide-column design may create separate tables for separate access patterns.

Example:

```text
messages_by_conversation
messages_by_sender
```

This duplicates data, but each table serves a specific query efficiently.

---

## 6.4 Trade-offs

- Poor support for joins
- Ad hoc queries are limited
- Data duplication is expected
- Partition-key mistakes create hotspots
- Large partitions become difficult to manage
- Cross-partition transactions are limited or expensive
- Data modeling must be driven by known queries

---

## Interview Language

> "Messages are written at high volume and retrieved by conversation ID in timestamp order. I would partition by conversation ID and cluster by message timestamp, while monitoring for very large or unusually hot conversations."

---

# 7. Graph Databases

A graph database models:

- Nodes
- Edges
- Properties

Example:

```text
(User)-[FOLLOWS]->(User)
(User)-[WORKS_AT]->(Company)
(Product)-[BELONGS_TO]->(Category)
```

---

## 7.1 Best Fit

Graph databases fit when:

- Relationships are first-class data
- Multi-hop traversal is frequent
- Relationship depth is not always known in advance
- The query asks how entities are connected

Common examples:

- Social networks
- Fraud detection
- Knowledge graphs
- Recommendation paths
- Network and dependency analysis
- Identity and access relationships

---

## 7.2 Why Graph Traversal Helps

A relational database can model graphs using join tables.

For shallow and predictable relationships, that may be sufficient.

A graph database becomes more attractive when queries look like:

```text
Find friends of friends
who work at companies
connected to a target organization
within three hops
```

The graph model makes traversal central rather than reconstructing relationships through repeated joins.

---

## 7.3 Trade-offs

- Not ideal for every storage need
- Horizontal scaling may be difficult depending on traversal patterns
- Partitioning connected graphs is challenging
- Operational expertise may be less common
- Aggregations and tabular reporting may fit other systems better
- A graph database may become a specialized secondary store rather than the source of truth

---

## Interview Language

> "The dominant query is multi-hop relationship traversal, so a graph model may simplify those queries. I would still consider a relational database if the traversal depth is shallow and predictable."

---

# 8. Comparing the Families

| Family | Best Access Pattern | Common Strength | Main Limitation |
|---|---|---|---|
| Document | Retrieve one aggregate/document | Flexible nested structure | Cross-document relationships |
| Key-value | Lookup by exact key | Very low latency | Limited querying |
| Wide-column | Partition-key lookup plus ordered range scan | High distributed write throughput | Query-driven modeling |
| Graph | Traverse relationships | Multi-hop relationship queries | Partitioning and specialized operations |

---

# 9. SQL versus NoSQL

The decision is rarely:

```text
SQL is old
NoSQL is modern
```

The real comparison is:

| Question | Relational | NoSQL |
|---|---|---|
| Rich joins required? | Strong fit | Often limited |
| Multi-row transactions required? | Strong fit | Varies |
| Flexible aggregate structure? | Possible | Often natural |
| Exact-key access dominates? | Possible | Key-value may be simpler |
| Massive distributed writes? | Possible with effort | Some families are designed for it |
| Ad hoc queries important? | Strong fit | Often weaker |
| Relationship traversal dominates? | Possible with joins | Graph may fit better |
| Data duplication acceptable? | Usually minimized | Often expected |

---

## 9.1 Use Relational When

- Business invariants span multiple records
- Transactions and constraints matter
- Joins are central
- Query patterns may evolve
- Data integrity matters more than flexible shape
- The expected scale fits one primary or manageable sharding

---

## 9.2 Use NoSQL When

- The access pattern strongly matches one NoSQL family
- Horizontal distribution is a first-class requirement
- Joins and multi-record transactions are limited
- Denormalization is acceptable
- Query patterns are predictable
- The chosen consistency model is acceptable

---

## 9.3 Hybrid Systems Are Normal

A system may use multiple stores:

```text
Orders and Payments
        ↓
Relational Database

Product Catalog
        ↓
Document Database

Sessions
        ↓
Key-Value Store

Search
        ↓
Search Index

Analytics
        ↓
Warehouse
```

Each store has a defined responsibility.

The relational database may remain the source of truth while other stores contain derived data.

---

## ⭐ Source-of-Truth Rule

For every database in the design, be able to answer:

1. Is this the source of truth?
2. Is this derived data?
3. How is it updated?
4. What happens when it is stale?
5. How is it rebuilt after failure?

This prevents vague architectures where several databases appear to own the same data.

---

# 10. Consistency Trade-offs

Distributed NoSQL databases may provide different consistency options.

Possible models include:

- Strong consistency
- Eventual consistency
- Tunable consistency
- Per-item atomicity
- Limited transaction scope

Do not assume all NoSQL systems are eventually consistent.

Instead ask:

- Must users immediately read their own writes?
- Can stale data cause financial or business harm?
- Is consistency required globally or only within one entity?
- Can conflicts be resolved later?
- What latency and availability trade-off is acceptable?

---

## Example

A profile-picture update may tolerate a few seconds of staleness.

A seat reservation cannot tolerate two users owning the same seat.

Therefore:

```text
Profile image metadata
→ Eventual consistency may be acceptable

Seat ownership
→ Strong atomic consistency is required
```

The database decision may differ even inside the same system.

---

# 11. Partition-Key Design

Partition-key choice is one of the most important NoSQL decisions.

A good partition key should:

- Have high cardinality
- Distribute storage evenly
- Distribute traffic evenly
- Match dominant queries
- Remain stable
- Avoid unbounded partitions

---

## 11.1 Hot Partition Example

Suppose all events for one day use:

```text
partition_key = date
```

If most traffic arrives today, all writes go to one partition.

A better design may combine fields:

```text
partition_key = date + hash(device_id)
```

This distributes writes, but queries for the entire day must read multiple partitions.

That is the trade-off:

```text
Better distribution
        versus
Simpler queries
```

---

## 11.2 High Cardinality Is Not Enough

A key can distribute stored rows evenly but still distribute traffic poorly.

Example:

```text
partition_key = celebrity_user_id
```

There may be millions of users, but one celebrity account may receive most traffic.

Design for both:

- Data distribution
- Request distribution

---

# 12. Denormalization and Duplication

NoSQL designs often duplicate data.

Example:

```text
order_by_id
order_by_user
order_by_status
```

The same order may appear in multiple query-oriented representations.

Benefits:

- Fast reads
- No joins
- Predictable access

Costs:

- Multiple writes
- Eventual consistency
- Repair and reconciliation
- More storage
- Harder schema evolution

---

## Keeping Copies Consistent

Common approaches:

- Write all copies synchronously when transaction support allows
- Publish events and update derived views asynchronously
- Use an outbox pattern
- Run repair or reconciliation jobs
- Make consumers idempotent

The system should define which copy is authoritative.

---

# 13. Common Design Mistakes

## Mistake 1: Choosing NoSQL Only for Scale

Relational databases can scale far beyond small systems.

Choose NoSQL because its data model and access pattern fit—not because the word “scale” appears in the requirements.

---

## Mistake 2: Calling Flexible Schema “No Schema”

Production systems still require:

- Validation
- Versioning
- Migration strategy
- Backward compatibility

Schema flexibility shifts responsibility; it does not remove it.

---

## Mistake 3: Ignoring Query Limitations

A design may support:

```text
Get order by order_id
```

but fail to support:

```text
Find all unpaid orders in one region from last month
```

List the required queries before choosing the model.

---

## Mistake 4: Choosing a Low-Cardinality Partition Key

Examples:

- Country
- Status
- Boolean flag
- Current date alone

These can create large or hot partitions.

---

## Mistake 5: Treating Every NoSQL Database as Eventually Consistent

Consistency depends on the technology and operation.

State the required guarantee instead of generalizing.

---

## Mistake 6: Using Multiple Databases Without Ownership Rules

Every store must have a clear role:

```text
Source of truth
Derived index
Cache
Analytics copy
Archive
```

---

# 14. Worked Example – Product Catalog

## Requirements

- Products have category-specific attributes
- Product details are read frequently
- Products are usually retrieved as complete records
- Search by keyword and filters is required
- Price and inventory correctness matter

---

## Storage Decision

### Product Description and Attributes

A document database may fit:

```text
Product document
- core fields
- category
- nested attributes
- media references
```

Why:

- Attributes vary by category
- Product data is retrieved as one aggregate
- Schema evolves over time

### Search

Use a search index as derived data.

Why:

- Full-text search
- Faceting
- Relevance ranking

### Price and Inventory

Use relational storage when transactional correctness is required.

Why:

- Prevent invalid prices
- Prevent overselling
- Preserve transactional updates

---

## Architecture

```text
Relational Database
Price and Inventory
        │
        ├── Product events
        ▼
Document Database
Catalog representation
        │
        ├── Index updates
        ▼
Search Index
```

The exact source of truth must be defined. One possible design is:

- Relational database: authoritative price and inventory
- Document database: authoritative descriptive catalog data
- Search index: derived and rebuildable

---

## Trade-offs

- Catalog data is spread across multiple stores
- Search updates may lag
- Product responses may combine data from more than one source
- Reconciliation is needed when events fail
- Operational complexity increases

---

# 15. Worked Example – Messaging History

## Access Patterns

- Write a message to a conversation
- Get the latest 50 messages for a conversation
- Continue from a cursor
- Preserve message order within a conversation

---

## Possible Wide-Column Model

```text
Partition key:
conversation_id

Clustering key:
message_timestamp + message_id
```

Why:

- Messages are retrieved by conversation
- Messages are ordered by time
- Writes can be distributed across conversations

---

## Risk

A conversation with extremely high activity can become a hot or oversized partition.

Possible mitigation:

```text
partition key:
conversation_id + time_bucket
```

Example:

```text
conversation_123:2026-07
```

Trade-off:

- Better partition size control
- Reading long history requires querying multiple buckets

---

# 16. Decision Framework

Use this sequence in an interview.

## Step 1 – List the Access Patterns

```text
Get by exact key?
Retrieve one aggregate?
Scan a time range?
Traverse relationships?
Run joins and ad hoc queries?
```

## Step 2 – Identify the Guarantees

```text
Strong consistency?
Multi-record transaction?
Read-after-write?
Ordering?
Uniqueness?
```

## Step 3 – Estimate Scale

```text
Read volume
Write volume
Data size
Growth rate
Hot keys
Geographic distribution
```

## Step 4 – Select the Simplest Family

```text
Relational
Document
Key-value
Wide-column
Graph
```

## Step 5 – State the Trade-off

```text
What queries become harder?
What consistency is relaxed?
Where is data duplicated?
What operational complexity is added?
```

---

# 17. Interview Language

Instead of:

> "I would use NoSQL because it scales."

Say:

> "The workload is dominated by direct session lookups using a known key, and sessions expire independently. A key-value store fits that access pattern and provides low-latency access, but it limits ad hoc queries."

Instead of:

> "The schema is flexible, so I would use a document database."

Say:

> "Product records have category-specific nested attributes and are usually retrieved as complete aggregates. A document model fits those reads, although price and inventory may remain in relational storage because they require stronger transactional guarantees."

Instead of:

> "I would use a graph database for relationships."

Say:

> "The dominant queries require multi-hop traversal across relationships of variable depth. A graph model may simplify those traversals, though a relational database may still be sufficient for shallow, predictable joins."

---

# 18. Interview Checklist

Before proposing a NoSQL database, confirm:

- [ ] The dominant access patterns are identified
- [ ] The required consistency is stated
- [ ] Transaction scope is understood
- [ ] The partition key is justified
- [ ] Hot partitions are considered
- [ ] Query limitations are acknowledged
- [ ] Data duplication is intentional
- [ ] The source of truth is clear
- [ ] Failure and rebuild behavior is explained
- [ ] A relational database was considered first
- [ ] The trade-off is stated

---

# 19. Thirty-Second Recap

- NoSQL is a group of database families, not one data model.
- Start with access patterns, not products.
- Document databases fit aggregate-oriented, variable records.
- Key-value stores fit exact-key, low-latency access.
- Wide-column databases fit large distributed writes and partition-key range queries.
- Graph databases fit multi-hop relationship traversal.
- NoSQL often trades joins and ad hoc queries for predictable scale and access.
- Partition-key choice determines distribution and hotspot risk.
- Denormalization improves reads but increases consistency work.
- NoSQL does not automatically mean eventual consistency.
- Relational and NoSQL databases are often used together.
- Every store needs a clear source-of-truth role.

---

# 20. Practice Exercise

For each system, choose the most likely primary database family and explain one trade-off.

1. User sessions retrieved only by session ID
2. Product catalog with category-specific attributes
3. Billions of device events queried by device and time range
4. Friend-of-friend recommendations
5. Banking ledger with transfers and balance invariants
6. Conversation messages retrieved by conversation and timestamp
7. User preferences stored and retrieved as one object
8. Orders queried by customer, status, date, and payment state

Use:

```text
Access pattern:

Required guarantee:

Database family:

Partition key or primary key:

Trade-off:
```

---

## 💡 Key Takeaway

> Choose a NoSQL database only when its access model and distribution strategy fit the workload better than relational storage. The database family is the result of the reasoning—not the starting point.
