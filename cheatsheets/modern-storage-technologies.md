# Cheat Sheet – Modern Storage Technologies

> Learn storage by **category first**, then by **technology**.

---

# 🧠 Mental Model

```text
Data Characteristics
        │
        ▼
Storage Category
        │
        ▼
Technology
```

Don't memorize products.

Understand **why** they exist.

---

# 📚 Storage Categories

| Category | Best For | Common Technologies |
|----------|----------|---------------------|
| Relational Database | Structured, transactional data | PostgreSQL, MySQL, Amazon Aurora, SQL Server, Oracle |
| Document Database | Flexible schemas | MongoDB, Couchbase, Firestore |
| Key-Value Store | Simple key lookups | DynamoDB, Redis, Riak |
| Wide-Column Database | Massive write throughput | Cassandra, HBase, ScyllaDB |
| Object Storage | Large binary objects | Amazon S3, Google Cloud Storage, Azure Blob Storage |
| Cache | Frequently accessed data | Redis, Memcached |
| Search Engine | Full-text search | Elasticsearch, OpenSearch, Solr |
| Time-Series Database | Metrics & monitoring | InfluxDB, TimescaleDB |
| Graph Database | Relationship traversal | Neo4j, Amazon Neptune |
| Data Warehouse | Analytics & reporting | Snowflake, BigQuery, Amazon Redshift |

---

# 📋 Quick Comparison

| Storage | ACID | Flexible Schema | Horizontal Scaling | Large Files | Typical Latency |
|----------|------|----------------|--------------------|-------------|-----------------|
| Relational DB | ✅ | ❌ | Moderate | ❌ | Low |
| Document DB | Limited | ✅ | ✅ | ❌ | Low |
| Key-Value Store | Limited | N/A | ✅ | ❌ | Very Low |
| Wide-Column DB | Limited | Semi | ✅ | ❌ | Low |
| Object Storage | N/A | N/A | Massive | ✅ | Medium |
| Cache | N/A | N/A | ✅ | ❌ | Very Low |
| Search Engine | N/A | Flexible | ✅ | ❌ | Low |
| Time-Series DB | Limited | Structured | ✅ | ❌ | Low |
| Graph DB | ACID support varies | Structured | Moderate | ❌ | Low |
| Data Warehouse | N/A | Structured | Massive | ❌ | Higher |

---

# 🎯 When Should I Use It?

## Relational Database

Choose when:

- Transactions matter
- Relationships matter
- Data is structured
- Consistency is critical

Examples:

- Users
- Orders
- Payments
- File metadata

---

## Document Database

Choose when:

- Schema changes frequently
- Objects have many optional fields
- Relationships are limited

Examples:

- Product catalogs
- User profiles
- Configuration data

---

## Key-Value Store

Choose when:

- Data is accessed by a single key
- Extremely fast lookups are required

Examples:

- Sessions
- Feature flags
- Shopping carts

---

## Wide-Column Database

Choose when:

- Write throughput is enormous
- Data is distributed across many nodes
- Horizontal scaling is the priority

Examples:

- Event logs
- IoT telemetry
- Activity feeds

---

## Object Storage

Choose when storing:

- Images
- Videos
- PDFs
- Documents
- Backups

Object storage is optimized for durability and scalability—not querying.

---

## Cache

Choose when:

- Reads dominate writes
- Low latency is important
- Data is frequently requested

Examples:

- User profiles
- Popular posts
- Session data

Remember:

> Cache is an optimization—not the source of truth.

---

## Search Engine

Choose when:

- Users search by keywords
- Ranking matters
- Full-text search is required

Examples:

- Product search
- Article search
- Log search

---

## Time-Series Database

Choose when:

- Time is part of every query
- Data arrives continuously

Examples:

- CPU metrics
- Application monitoring
- Sensor data

---

## Graph Database

Choose when:

- Relationships are the primary focus
- Traversing connected data is common

Examples:

- Social networks
- Recommendation engines
- Fraud detection

---

## Data Warehouse

Choose when:

- Running analytical queries
- Processing historical data
- Building dashboards

Examples:

- Business intelligence
- Reporting
- Data analytics

---

# ⚡ Observation → Storage

| Observation | Storage Category |
|-------------|------------------|
| Structured business data | Relational Database |
| Flexible JSON documents | Document Database |
| Large images and videos | Object Storage |
| Frequently accessed data | Cache |
| Session storage | Key-Value Store |
| Massive event ingestion | Wide-Column Database |
| Keyword search | Search Engine |
| Metrics over time | Time-Series Database |
| Connected relationships | Graph Database |
| Historical reporting | Data Warehouse |

---

# ☕ Backend Java Lens

| Requirement | Typical Spring Ecosystem Choice |
|-------------|---------------------------------|
| Relational Database | Spring Data JPA + PostgreSQL / MySQL / Aurora |
| Document Database | Spring Data MongoDB |
| Cache | Spring Cache + Redis |
| Object Storage | AWS SDK (S3), GCP Storage Client, Azure Blob SDK |
| Search | Spring Data Elasticsearch |
| Messaging Events | Kafka + Event Store |
| Analytics | ETL pipelines into Snowflake / BigQuery / Redshift |

Remember:

Frameworks don't determine architecture.

Architecture determines which frameworks and technologies you integrate.

---

# 💡 Common Interview Examples

| System | Typical Storage Choices |
|---------|-------------------------|
| Google Drive | Object Storage + Relational Database + Cache |
| Instagram | Object Storage + Relational Database + Cache |
| YouTube | Object Storage + Relational Database + Search Engine + Cache |
| Twitter/X | Relational Database + Cache + Search Engine |
| WhatsApp | Relational Database + Object Storage + Cache |
| Uber | Relational Database + Cache + Time-Series Database |
| Amazon | Relational Database + Document Database + Search Engine + Cache |

---

# ⚠️ Common Mistakes

❌ Using one database for every type of data.

❌ Storing images directly in a relational database.

❌ Treating Redis as permanent storage.

❌ Choosing technologies before understanding the data.

❌ Memorizing products instead of storage categories.

---

# 📝 60-Second Review

- Structured data → Relational Database
- Flexible schema → Document Database
- Key lookups → Key-Value Store
- Large files → Object Storage
- Read-heavy → Cache
- Search → Search Engine
- Metrics → Time-Series Database
- Relationships → Graph Database
- Analytics → Data Warehouse

---

# 💡 Key Takeaway

> **Think in storage categories first. Technologies are implementations of those categories, not the architectural decision itself.**
