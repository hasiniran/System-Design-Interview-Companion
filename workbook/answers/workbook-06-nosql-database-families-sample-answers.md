# Workbook 06 – NoSQL Database Families: Sample Answers

> These are sample answers, not the only valid designs. In an interview, start from the access pattern and guarantees, then justify the database family and trade-off.

---

## Exercise 1 – Choose the Database Family

### Scenario 1 – User Sessions

```text
Access pattern:
Direct lookup by a known session_id. Sessions also have a natural expiration time.

Database family:
Key-value store.

Example:
Redis.

Why:
Sessions can be retrieved, updated, and invalidated with a low-latency key lookup.
TTL support maps naturally to session expiration.

Trade-off:
Limited ad hoc querying and filtering compared with relational databases.
```

### Scenario 2 – Product Catalog

```text
Access pattern:
Products are usually retrieved as complete aggregates, while attributes vary significantly by category.

Database family:
Document database.

Example:
MongoDB.

Why:
The document model naturally represents category-specific nested attributes and allows the product to be retrieved as one aggregate.

Trade-off:
Cross-document relationships and joins are less natural than in a relational database.
Denormalization may duplicate data, and schema validation still needs to be managed.
```

> Flexible attributes alone do not automatically require a document database. The aggregate-oriented access pattern is what makes the choice stronger.

### Scenario 3 – IoT Events

```text
Access pattern:
Very high continuous write volume.
Events are usually retrieved by device_id within a timestamp range.

Database family:
Wide-column database.

Example:
Cassandra.

Why:
Data can be partitioned by device_id and ordered by timestamp, supporting high distributed write throughput and efficient range queries.

Trade-off:
Ad hoc querying and joins are limited.
Data is often duplicated for different access patterns, and poor partition-key choices can create hotspots.
Cross-partition transactions may be limited or expensive.
```

### Scenario 4 – Friend Recommendations

```text
Access pattern:
Multi-hop relationship traversal such as friends-of-friends.

Database family:
Graph database.

Example:
Amazon Neptune.

Why:
Relationships are first-class entities, making variable-depth traversal more natural than repeatedly joining relational tables.

Trade-off:
Graph databases are specialized for relationship traversal and may be less suitable for traditional reporting and aggregation.
Partitioning highly connected graphs can also be difficult.
```

```text
Shallow + predictable relationships
→ Relational database may be sufficient

Deep / variable traversal
→ Graph database becomes more attractive
```

### Scenario 5 – Banking Transfers

```text
Access pattern:
Transactional operations across related records, together with reporting and reconciliation queries.

Database family:
Relational database.

Examples:
PostgreSQL, Aurora, Oracle.

Why:
Transfers require atomicity, integrity constraints, strong consistency, and durable transactional state.

Trade-off:
Horizontal write scaling and sharding introduce significant complexity, especially for transactions spanning shards.
```

### Scenario 6 – Messaging History

```text
Access pattern:
Large write volume with messages retrieved by conversation_id and ordered by timestamp.

Database family:
Wide-column database.

Example:
Cassandra.

Why:
conversation_id can be used as the partition key and timestamp as the clustering key, making ordered message retrieval within a conversation efficient.

Trade-off:
The schema is query-driven, so additional access patterns may require duplicated tables.
Very large or extremely active conversations can also create hot or oversized partitions.
```

---

## Exercise 2 – Access-Pattern-First Modeling

### 2.1 User Sessions

```text
Primary / partition key:
session_id

Why:
The query always starts with session_id, so using it as the key gives a direct single-partition lookup with low latency.
```

TTL helps with expiration and storage cleanup, but it does not by itself prevent hotspots.

### 2.2 Messaging History

```text
Partition key:
conversation_id

Clustering / sort key:
timestamp + message_id

Why:
conversation_id keeps one conversation's messages together.
timestamp supports ordered retrieval, while message_id gives deterministic ordering when two messages share the same timestamp.
```

If a conversation becomes too large or hot:

```text
conversation_id + time_bucket
```

Trade-off:

```text
Better partition-size control
→ long history may require multiple partition reads
```

### 2.3 IoT Events

```text
Partition key:
device_id + time_bucket

Example:
device_123 + 2026-08-25

Clustering / sort key:
timestamp

Why:
device_id matches the dominant query, the time bucket prevents unbounded partition growth, and timestamp supports efficient range scans.
```

Trade-off:

```text
Queries spanning multiple time buckets must read multiple partitions.
```

### 2.4 Product Documents

```text
Document key:
product_id

Embed:
- name
- description
- category
- category-specific attributes
- image URLs / metadata

Reference:
- authoritative price
- inventory
- unbounded collections such as reviews

Why:
Product details are usually retrieved as one aggregate, so embedding those fields avoids unnecessary joins.
Price and inventory require stronger transactional guarantees.
Reviews can grow without bound, so they should not be embedded directly in the product document.
```

Actual image binaries belong in object storage; the document should normally hold URLs or metadata.

Reviews might live in relational or wide-column storage depending on scale and access patterns.

---

## Exercise 3 – SQL or NoSQL?

### Scenario 1 – Customer Profile

```text
Decision:
NoSQL → Document database.

Reason:
The profile is usually read and updated as one aggregate, and preferences/settings fit naturally inside a nested document.
```

Example: MongoDB.

Cassandra would not be the right example here; Cassandra is a wide-column database.

### Scenario 2 – Orders

```text
Decision:
Relational database.

Reason:
The workload requires rich queries across customer, status, date, and payment data, together with transactional updates and reconciliation.
Relational storage provides strong transaction and integrity guarantees.
```

### Scenario 3 – Feature Flags

```text
Decision:
NoSQL → Key-value store.

Example:
Redis.

Reason:
The access pattern is a direct lookup using a known key such as user_id + feature_name, making a key-value store a natural low-latency fit.

Trade-off:
Limited query flexibility beyond key-based access.
```

### Scenario 4 – Product Descriptions + Inventory

```text
Product descriptions:
Document database.

Inventory:
Relational database.

Why use different stores:
Product descriptions have category-specific nested attributes and are usually retrieved as complete aggregates, which fits a document model.

Inventory has business invariants such as preventing negative stock and may require atomic updates during purchases, so relational storage is a better source of truth.
```

> Use multiple stores only when each one solves a clearly different requirement that justifies the extra operational complexity.

### Scenario 5 – Social Followers

```text
Decision:
Relational database.

Reason:
The dominant query is shallow and predictable:
"get all followers for user_id."

A relational join table with appropriate indexes can support this efficiently.
Since multi-hop graph traversal is uncommon, introducing a graph database would add unnecessary specialization.
```

Possible model:

```text
follows
----------------
follower_id
followed_user_id
created_at
```

Useful indexes:

```text
(followed_user_id, follower_id)
```

and optionally:

```text
(follower_id, followed_user_id)
```

---

## Exercise 4 – Partition-Key Judgment

### 4.1 IoT Events

```text
Good or risky:
Risky.

Why:
Using only date creates a low-cardinality partition key.
Most current traffic goes to today's partition, creating a hot partition.

Better key:
device_id + date

Why:
device_id distributes traffic across many partitions, while the date bucket prevents each device partition from growing indefinitely.
```

### 4.2 Messaging

```text
Good or risky:
Usually good for normal conversations, but risky for very large or highly active public conversations.

Why:
conversation_id matches the query well, but one extremely active conversation can create a hot and oversized partition.

Possible mitigation:
Keep conversation_id for normal/private conversations.

For high-volume public channels, use:
conversation_id + time_bucket
```

Trade-off:

```text
Better distribution and partition-size control
→ more implementation complexity and multi-partition history reads
```

### 4.3 User Events

```text
Good or risky:
Usually good, but risky for extremely hot users.

Why:
user_id matches user-centric access patterns, but celebrity users can concentrate traffic on a single partition.

Possible mitigation:
Introduce bucketing for exceptionally hot users.

Example:
user_id + bucket_id
```

Possible buckets:

```text
celebrity_123:0
celebrity_123:1
celebrity_123:2
celebrity_123:3
```

Trade-off:

```text
Better traffic distribution
→ reading all events for one user may require multiple partition reads
```

`user_id + region` is not automatically a good solution because one region may still contain most traffic, and region may not match the query pattern.

### 4.4 Orders

```text
Good or risky:
Very risky.

Why:
order_status has very low cardinality and only a few possible values.
Data and traffic are likely to be unevenly distributed, creating large or hot partitions.

Better key:
Depends on the access pattern.
```

If the dominant query is:

```text
Get order by order_id
```

use:

```text
partition_key = order_id
```

If the dominant query is:

```text
Get all orders for customer_id
```

use:

```text
partition_key = customer_id
sort_key = created_at
```

If a customer's history becomes too large:

```text
customer_id + time_bucket
```

may be appropriate.

Avoid:

```text
order_id + order_status
```

because `order_status` changes over time.

A good partition key should usually be:

- high cardinality
- stable
- aligned with the dominant query
- evenly distributed by storage
- evenly distributed by traffic
- resistant to hotspots
- bounded in size

---

## Exercise 5 – Source of Truth

| Store | Source of Truth or Derived? | What Happens If It Is Stale? | Can It Be Rebuilt? |
|---|---|---|---|
| Relational DB | Source of truth for orders, payments, inventory, or price depending on ownership | Staleness is generally unacceptable for authoritative transactional data | Recover from replicas, backups, and point-in-time recovery; not from derived stores |
| Document DB | Source of truth for descriptive product/catalog data | Staleness is generally unacceptable for the fields it owns | Recover from backups/replication; not from derived stores |
| Search index | Derived | Search may temporarily show old product data or omit recent updates | Yes, from the authoritative product store |
| Cache | Derived | Users may receive outdated values until refresh or expiration | Yes, repopulate from the source of truth |

Important distinction:

```text
Stale cache
→ outdated / incorrect value

Cache miss
→ slower read because the source of truth must be queried
```

### 5.1 Ownership Question

```text
If two databases are both treated as the source of truth for the same field, they can disagree after partial failures or delayed updates.

The system then has no authoritative value to resolve the conflict, which can cause inconsistent user experiences and incorrect business operations.

Each piece of data should have one authoritative owner.
Derived stores may be stale because the system knows where to retrieve the correct value.
```

This is valid:

```text
Relational DB
→ source of truth for price and inventory

Document DB
→ source of truth for descriptive catalog data
```

because the stores own different fields.

---

## Exercise 6 – Two-Minute Interview Response

### Product Catalog

```text
Relational data:
Inventory and authoritative product price.

Document data:
Product name, description, category, category-specific attributes, and image references.

Why:
Product details are usually retrieved as one aggregate, and category-specific attributes vary significantly, so a document database fits the catalog model.

Inventory and authoritative price participate in business invariants and require transactional correctness, so relational storage is a better authoritative store for those fields.

Source of truth:
Document DB → descriptive catalog data
Relational DB → inventory and authoritative price

Primary / document key:
product_id

Search:
Use a search index for keyword search, faceting, and relevance ranking.
The search index is derived from the authoritative catalog data and may lag by a few seconds.

Consistency:
Search results, cache entries, and some descriptive product views may be eventually consistent.

Inventory and authoritative price checks require strong consistency at the source of truth.
Before checkout, validate price and inventory against the relational database.

Trade-off:
Using multiple stores increases operational complexity because the system must synchronize data, handle partial failures and stale derived views, reconcile inconsistencies, and sometimes combine results from multiple stores.
```

---

## Rapid Recall – Sample Answers

1. **What is the biggest mistake when choosing NoSQL?**  
   Choosing NoSQL before understanding the access pattern and required guarantees.

2. **What should come before choosing a database family?**  
   Understand the dominant access patterns, business invariants, consistency needs, and expected scale.

3. **When does a document database fit well?**  
   When related data is usually retrieved as one aggregate, especially when the structure varies between records.

4. **When does a key-value store fit well?**  
   When the dominant operation is a fast lookup using a known key.

5. **When does a wide-column database fit well?**  
   When the system has very high distributed write volume and predictable queries using a partition key, often with ordered or range-based reads within a partition.

6. **When does a graph database fit well?**  
   When multi-hop or variable-depth relationship traversal is a dominant query pattern.

7. **What makes a good partition key?**  
   A good key has high cardinality, is stable, aligns with the access pattern, distributes both data and traffic evenly, and avoids hotspots or unbounded partitions.

8. **Why is denormalization common in NoSQL systems?**  
   To serve known read patterns without expensive joins, often by duplicating data into query-specific representations.

9. **Why does NoSQL not automatically mean eventual consistency?**  
   Different NoSQL databases provide different consistency guarantees. Some support strong consistency, tunable consistency, or transactional operations, while others favor eventual consistency.

10. **Why must the source of truth be explicit?**  
    To avoid conflicting authoritative values for the same data and to make it clear how derived stores are repaired or rebuilt.

---

## Final Reflection – Sample Answers

### Which NoSQL family still feels least automatic?

A reasonable answer:

```text
Wide-column databases, especially choosing partition keys and deciding when time bucketing is necessary.
```

### What is the most important rule to remember in an interview?

```text
Start from the access pattern and guarantees, then choose the database family.
Do not choose NoSQL simply because the system needs to scale.
```

---

## Key Takeaway

> Query first → key second → hotspot check third.

And:

> Choose the database family because its access model and guarantees fit the workload—not because of the technology name.
