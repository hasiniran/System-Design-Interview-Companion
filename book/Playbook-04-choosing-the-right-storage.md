# Playbook 04 – Choosing the Right Storage

> Good system designers don't start by asking:
>
> **"Should I use PostgreSQL or MongoDB?"**
>
> They first ask:
>
> **"What kind of data am I storing?"**

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 20–25 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Interview Expectation** | Architecture Decisions |
| **Difficulty** | Intermediate |
| **Prerequisites** | Playbook 03 – Capacity Estimation |

---

# 📌 What Problem Does This Solve?

Once you've estimated the scale of your system, the next interview question is almost always:

> **Where should the data live?**

Many candidates immediately choose a database they know.

Strong candidates first understand the data.

Choosing the wrong storage technology can lead to poor scalability, unnecessary complexity, and expensive migrations later.

---

# 🧠 Mental Model

```text
Understand Product
        │
        ▼
Requirements
        │
        ▼
Workload Characteristics
        │
        ▼
Capacity Estimation
        │
        ▼
Understand the Data
        │
        ▼
Storage Requirements
        │
        ▼
Storage Choice
```

Storage is a consequence of the data—not personal preference.

---

# Start with the Data

Every product stores different kinds of data.

Example:

## Dropbox

| Data | Characteristics |
|------|-----------------|
| File contents | Large binary objects |
| File metadata | Structured |
| Users | Structured |
| Permissions | Relational |
| Sharing links | Structured |

Notice that not every piece of data belongs in the same storage system.

---

# Data Characteristics

Before choosing storage, identify the characteristics of your data.

| Characteristic | Typical Storage |
|----------------|-----------------|
| Structured | Relational Database |
| Flexible schema | Document Database |
| Large binary objects | Object Storage |
| Frequently accessed | Cache |
| Transactional | Relational Database |
| Massive write throughput | Wide-column / Partitioned Database |
| Analytics | Data Warehouse |

Ask yourself:

- Is the data structured?
- Does it have relationships?
- Does it require transactions?
- Is it large?
- Does it change frequently?
- Is it queried often?
- Does it require long-term retention?

Every answer influences storage selection.

---

# Common Storage Categories

Instead of memorizing products, learn storage categories.

| Category | Best For |
|----------|----------|
| Relational Database | Structured, transactional data |
| Document Database | Flexible schemas |
| Key-Value Store | Fast lookups |
| Object Storage | Files, videos, images |
| Cache | Frequently accessed data |
| Data Warehouse | Analytics and reporting |

Think in categories first.

Products come later.

---

# Choosing the Right Storage

Think about the data, not the technology.

## Relational Database

Use when:

- Data is structured
- Relationships matter
- Transactions are required
- Strong consistency is important

Examples:

- Users
- Orders
- Metadata
- Permissions

---

## Object Storage

Use when storing:

- Images
- Videos
- PDFs
- Documents
- Backups

Object storage is optimized for storing large binary objects—not querying them.

---

## Document Database

Useful when:

- Objects have flexible schemas
- Different records contain different fields
- Relationships are limited

Examples:

- Product catalogs
- User preferences
- Configuration documents

---

## Key-Value Store

Best when you already know the key.

Examples:

- Sessions
- User preferences
- Feature flags

Fast lookups.

Simple access patterns.

---

## Cache

Use for data that is:

- Frequently accessed
- Expensive to compute
- Safe to temporarily duplicate

Remember:

A cache is not the source of truth.

---

## Data Warehouse

Optimized for:

- Historical analysis
- Business intelligence
- Reporting
- Dashboards

Not for serving application traffic.

---

# Storage Decision Tree

```text
Large binary object?

        │
   Yes ─────────► Object Storage

        │
       No

        ▼

Structured?

        │
   Yes

        ▼

Need transactions?

        │
   Yes ─────────► Relational Database

        │
       No

        ▼

Flexible schema?

        │
   Yes ─────────► Document Database

        │
       No

        ▼

Need very fast key lookups?

        │
   Yes ─────────► Key-Value Store
```

This won't solve every interview problem, but it's an excellent starting point.

---

# Worked Example – Google Drive

Google Drive stores several different types of data.

| Data | Storage |
|------|---------|
| File contents | Object Storage |
| File metadata | Relational Database |
| Users | Relational Database |
| Permissions | Relational Database |
| Frequently accessed metadata | Cache |

Notice that a single system often uses multiple storage technologies.

This is normal.

---

# Storage vs Cache

One common interview mistake is confusing storage with caching.

| Storage | Cache |
|----------|-------|
| Source of truth | Temporary copy |
| Durable | Usually volatile |
| Long-term | Short-term |
| Required | Optional optimization |

Never replace your database with a cache.

---

# ⭐ Interview Pattern

When choosing storage, explain your reasoning.

Instead of saying:

> "I'd use PostgreSQL."

Say:

> "User accounts are structured, highly relational, and require transactions. Therefore, I'd store them in a relational database."

Instead of:

> "I'd use S3."

Say:

> "Uploaded files are large binary objects, so object storage is the most appropriate choice."

Notice the pattern:

> **Observation → Requirement → Storage Decision**

---

# ✅ Interview Checklist

- □ Did I identify every type of data?
- □ Did I separate binary objects from metadata?
- □ Did I justify each storage choice?
- □ Did I avoid selecting technologies based on familiarity?
- □ Did I explain why my choice fits the data?

---

# ⚠️ Interview Traps

- Choosing one database for everything.
- Storing large files inside a relational database.
- Confusing cache with persistent storage.
- Selecting technologies before understanding the data.
- Forgetting that one system can use multiple storage technologies.

---

# ☕ Backend Java Lens

A Spring Boot application commonly integrates with several storage technologies.

| Storage Category | Common Technologies |
|------------------|---------------------|
| Relational Database | PostgreSQL, MySQL, Amazon Aurora |
| Object Storage | Amazon S3, Google Cloud Storage, Azure Blob Storage |
| Cache | Redis |
| Search | Elasticsearch / OpenSearch |
| Analytics | BigQuery, Snowflake, Redshift |

Notice that each storage technology solves a different problem.

---

# 📝 Whiteboard Sketch

```text
Product

↓

Requirements

↓

Workload Characteristics

↓

Capacity Estimation

↓

Understand the Data

↓

Choose Storage

↓

Architecture
```

---

# 🎯 30-Second Recap

- Storage decisions begin with understanding the data.
- Different data types often require different storage systems.
- Binary objects belong in object storage.
- Structured, transactional data belongs in relational databases.
- Caches improve performance but are not the source of truth.
- Explain every storage decision using the characteristics of the data.

---

# 💬 Practice Exercise

Design Instagram.

List every major type of data.

For each one:

1. Describe its characteristics.
2. Choose the appropriate storage.
3. Explain your reasoning.
4. Identify one trade-off.

---

# 💡 Key Takeaway

> **Choose storage based on the data, not the database you know best.**
