# Cheat Sheet – Storage Decision Tree

> Don't start with the database.
>
> Start with the **data**.

---

# 🧠 Decision Framework

```text
Understand the Data
        │
        ▼
Identify Data Characteristics
        │
        ▼
Determine Storage Requirements
        │
        ▼
Choose Storage Category
        │
        ▼
Select Technology
```

**Interview Rule**

> Observation → Requirement → Storage Decision

---

# 🌳 Storage Decision Tree

```text
                           What am I storing?
                                   │
        ┌──────────────────────────┴──────────────────────────┐
        │                                                     │
Large binary objects?                                  Structured data?
        │                                                     │
   Yes ─┴─► Object Storage                              Yes ───┴──► Need ACID transactions?
                                                              │
                                                      Yes ────┴────► Relational Database
                                                              │
                                                             No
                                                              │
                                                     Flexible schema?
                                                              │
                                                      Yes ────┴────► Document Database
                                                              │
                                                             No
                                                              │
                                                    Key-based lookups?
                                                              │
                                                      Yes ────┴────► Key-Value Store
```

---

# 📋 Storage Categories

| Data Characteristic | Storage Category |
|---------------------|------------------|
| Structured data | Relational Database |
| Strong relationships | Relational Database |
| ACID transactions | Relational Database |
| Flexible schema | Document Database |
| Key-based lookups | Key-Value Store |
| Large binary objects | Object Storage |
| Frequently accessed data | Cache |
| Historical analytics | Data Warehouse |
| Searchable text | Search Engine |
| Massive write throughput | Wide-Column Database |
| Time-series data | Time-Series Database |
| Graph relationships | Graph Database |

---

# 🔍 Observation → Decision

| Observation | Storage Decision |
|-------------|------------------|
| User accounts | Relational Database |
| Orders | Relational Database |
| Product catalog (changing attributes) | Document Database |
| Uploaded images | Object Storage |
| Videos | Object Storage |
| PDFs | Object Storage |
| User sessions | Cache / Key-Value Store |
| API response cache | Cache |
| Feature flags | Key-Value Store |
| Search index | Search Engine |
| Event logs | Wide-Column Database |
| Metrics & monitoring | Time-Series Database |
| Social network relationships | Graph Database |
| BI reports | Data Warehouse |

---

# ⚡ Quick Decision Guide

## Need transactions?

✅ Relational Database

---

## Need joins?

✅ Relational Database

---

## Storing files?

✅ Object Storage

---

## Need flexible schema?

✅ Document Database

---

## Need sub-millisecond lookups?

✅ Cache or Key-Value Store

---

## Need full-text search?

✅ Search Engine

---

## Need analytics?

✅ Data Warehouse

---

## Need massive write throughput?

✅ Wide-Column Database

---

## Need relationship traversal?

✅ Graph Database

---

## Need time-series queries?

✅ Time-Series Database

---

# ⚖️ Trade-offs

| Storage | Strength | Trade-off |
|----------|----------|-----------|
| Relational Database | Strong consistency, transactions | Harder to scale horizontally |
| Document Database | Flexible schema | Weaker relationships |
| Key-Value Store | Extremely fast lookups | Limited querying |
| Object Storage | Massive scalability | Not queryable |
| Cache | Very low latency | Not durable |
| Data Warehouse | Analytical queries | Not for OLTP workloads |
| Search Engine | Full-text search | Eventually consistent indexing |
| Wide-Column Database | High write throughput | More complex data modeling |
| Graph Database | Relationship traversal | Specialized use cases |
| Time-Series Database | Time-based analytics | Limited general-purpose querying |

---

# 🚩 Interview Patterns

Instead of saying:

> "I'll use PostgreSQL."

Say:

> "User accounts require structured data, relationships, and transactions, so I'll use a relational database."

---

Instead of:

> "I'll use S3."

Say:

> "Uploaded files are large binary objects, so object storage is the appropriate choice."

---

Instead of:

> "I'll use Redis."

Say:

> "This data is frequently accessed and latency-sensitive, so I'll introduce a cache."

---

# 🧩 Common System Examples

| System | Storage Choices |
|---------|-----------------|
| Google Drive | Object Storage + Relational Database + Cache |
| Instagram | Object Storage + Relational Database + Cache |
| YouTube | Object Storage + Relational Database + CDN + Cache |
| Twitter | Relational Database + Cache + Search Engine |
| WhatsApp | Relational Database + Object Storage + Cache |
| Uber | Relational Database + Cache + Time-Series Database |

---

# ✅ Interview Checklist

Before choosing storage, ask yourself:

- □ What types of data does this system store?
- □ Is the data structured or unstructured?
- □ Are transactions required?
- □ Are there relationships between records?
- □ Are there large binary objects?
- □ Does the schema change frequently?
- □ Is low latency critical?
- □ Is this operational data or analytical data?

---

# 💡 Key Takeaway

> **Storage is chosen based on the characteristics of the data—not the popularity of the technology.**
