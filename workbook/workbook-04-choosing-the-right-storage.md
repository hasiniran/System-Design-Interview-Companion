# Workbook 04 – Choosing the Right Storage

> Practice choosing storage based on data characteristics, access patterns, requirements, and trade-offs—not technology familiarity.

---

## 📖 Study Card

|                        |                                          |
| ---------------------- | ---------------------------------------- |
| **Estimated Time**     | 45–60 minutes                            |
| **Companion Playbook** | Playbook 04 – Choosing the Right Storage |
| **Difficulty**         | Intermediate                             |
| **Exercises**          | 5                                        |

---

# 🎯 Learning Objectives

After completing this workbook, you should be able to:

* Identify the major data types in a system.
* Distinguish data characteristics from workload characteristics.
* Translate storage requirements into storage categories.
* Choose appropriate storage for different data types.
* Recognize when a system needs multiple storage technologies.
* Explain the trade-offs introduced by each storage decision.

---

# Before You Begin

Review:

* Playbook 04 – Choosing the Right Storage
* Storage Decision Tree Cheat Sheet
* Modern Storage Technologies Cheat Sheet
* Architecture Decision Matrix

Recommended timing:

| Exercise | Time      |
| -------- | --------- |
| 1        | 5 min     |
| 2        | 10 min    |
| 3        | 10 min    |
| 4        | 10 min    |
| 5        | 15–20 min |

Don't begin with:

> "I'll use PostgreSQL."

Begin with:

> "What am I storing, how is it accessed, and what guarantees does it need?"

---

# Exercise 1 – Identify the Data

For each system, list the major data types that may need storage.

Do **not** choose technologies yet.

---

## Google Drive

| Data Type | Example |
| --------- | ------- |
|           |         |
|           |         |
|           |         |
|           |         |
|           |         |

---

## Instagram

| Data Type | Example |
| --------- | ------- |
|           |         |
|           |         |
|           |         |
|           |         |
|           |         |

---

## Food Delivery Platform

| Data Type | Example |
| --------- | ------- |
|           |         |
|           |         |
|           |         |
|           |         |
|           |         |

---

### Reflection

Choose one system above.

Why would storing all of its data in one storage system be limiting?

```text
____________________________________________________

____________________________________________________

____________________________________________________
```

---

# Exercise 2 – Data Characteristics

For each data type, identify the characteristics that matter most.

Use characteristics such as:

* Structured
* Unstructured
* Large binary object
* Strong relationships
* Transactional
* Flexible schema
* Key-based access
* Frequently accessed
* Searchable text
* Time-series
* Analytical

| Data                     | Characteristics |
| ------------------------ | --------------- |
| User accounts            |                 |
| Order records            |                 |
| Uploaded videos          |                 |
| Product catalog          |                 |
| User sessions            |                 |
| File metadata            |                 |
| Application metrics      |                 |
| Article search index     |                 |
| Social graph             |                 |
| Historical sales reports |                 |

---

### Reflection

Choose one row above.

Which characteristic had the greatest influence on your eventual storage decision?

```text
____________________________________________________

____________________________________________________
```

---

# Exercise 3 – Storage Category Selection

Choose the most appropriate **storage category**.

Do not name a specific product unless needed to explain your reasoning.

| Requirement                                       | Storage Category | Why? |
| ------------------------------------------------- | ---------------- | ---- |
| Bank transfers require ACID transactions          |                  |      |
| Users upload large videos                         |                  |      |
| Product attributes vary significantly by category |                  |      |
| Session data is accessed by session ID            |                  |      |
| Users search millions of articles by keywords     |                  |      |
| Billions of events arrive continuously            |                  |      |
| CPU metrics are queried by time range             |                  |      |
| Analysts run historical sales reports             |                  |      |
| Friend-of-friend traversal is common              |                  |      |
| Popular profiles are repeatedly requested         |                  |      |

---

### Reflection

Which decision above was the least obvious?

```text
____________________________________________________
```

What additional question would you ask before finalizing that choice?

```text
____________________________________________________

____________________________________________________
```

---

# Exercise 4 – Polyglot Persistence

## Scenario

Design the storage layer for Instagram.

Assume the system supports:

* User accounts
* Photo uploads
* Captions
* Likes
* Comments
* Following relationships
* Home feed
* Search
* Notifications

For each data type:

1. Identify its important characteristics.
2. Choose a storage category.
3. Explain why.
4. Identify one trade-off.

| Data                          | Data Characteristics | Storage Category | Why? | Trade-off |
| ----------------------------- | -------------------- | ---------------- | ---- | --------- |
| User accounts                 |                      |                  |      |           |
| Photos                        |                      |                  |      |           |
| Photo metadata                |                      |                  |      |           |
| Likes                         |                      |                  |      |           |
| Comments                      |                      |                  |      |           |
| Following relationships       |                      |                  |      |           |
| Search index                  |                      |                  |      |           |
| Frequently accessed feed data |                      |                  |      |           |

---

## Architecture Summary

Which data should remain in the primary source of truth?

```text
____________________________________________________

____________________________________________________
```

Which data can be derived or rebuilt?

```text
____________________________________________________

____________________________________________________
```

Which data would you cache?

```text
____________________________________________________

____________________________________________________
```

---

## Biggest Storage Constraint

Choose one:

* Storage scale
* Transactional consistency
* Query flexibility
* Read latency
* Write throughput
* Relationship traversal
* Search
* Other

```text
____________________________________________________
```

Why?

```text
____________________________________________________

____________________________________________________
```

---

# ⚡ Quick Fire

For each observation, write the first **storage category** that comes to mind.

| Observation                   | Storage Category |
| ----------------------------- | ---------------- |
| ACID transactions             |                  |
| Strong relationships          |                  |
| Large binary files            |                  |
| Flexible JSON documents       |                  |
| Lookup by known key           |                  |
| Frequently accessed data      |                  |
| Full-text keyword search      |                  |
| Historical analytics          |                  |
| Massive write throughput      |                  |
| Metrics over time             |                  |
| Relationship traversal        |                  |
| Long-term file retention      |                  |
| Structured metadata           |                  |
| Temporary session data        |                  |
| Business intelligence reports |                  |

---

### Reflection

Choose one answer above and explain why you selected it.

```text
____________________________________________________

____________________________________________________
```

---

# 🧩 Observation → Requirement → Storage Decision

Complete each statement.

---

User accounts have a stable schema, strong relationships, and transactional updates.

> Therefore, I need ________________________________, so I will choose ________________________________.

---

Users upload multi-gigabyte videos.

> Therefore, I need ________________________________, so I will choose ________________________________.

---

Product records contain different attributes depending on category.

> Therefore, I need ________________________________, so I will choose ________________________________.

---

Sessions are retrieved using a known session ID and require very low latency.

> Therefore, I need ________________________________, so I will choose ________________________________.

---

Users search millions of documents by keywords and relevance.

> Therefore, I need ________________________________, so I will choose ________________________________.

---

Analysts run aggregations over years of historical data.

> Therefore, I need ________________________________, so I will choose ________________________________.

---

# Exercise 5 – Interview Challenge

Choose **ONE** system:

* YouTube
* Dropbox
* E-commerce Platform

Using the Interview Worksheet, focus specifically on storage design.

Cover:

* Product summary
* Functional requirements
* Non-functional requirements
* Workload characteristics
* Capacity estimates that influence storage
* Major data types
* Data characteristics
* Storage requirements
* Storage category for each data type
* Source-of-truth decisions
* Cache or derived-data decisions
* Biggest storage constraint
* Trade-offs

---

## Storage Decision Log

| Observation | Requirement | Storage Decision | Trade-off |
| ----------- | ----------- | ---------------- | --------- |
|             |             |                  |           |
|             |             |                  |           |
|             |             |                  |           |
|             |             |                  |           |
|             |             |                  |           |

---

# 💭 Reflection

Which data type was hardest to place?

```text
____________________________________________________
```

Which storage decision influenced your architecture the most?

```text
____________________________________________________
```

Where did you consider more than one reasonable storage category?

```text
____________________________________________________

____________________________________________________
```

Which trade-off are you least confident about?

```text
____________________________________________________
```

What would you ask the interviewer before finalizing the storage design?

```text
____________________________________________________

____________________________________________________
```

---

# Common Mistakes

* Choosing a technology before understanding the data.
* Using one database for every data type.
* Confusing workload characteristics with data characteristics.
* Storing large binary objects in a relational database by default.
* Treating a cache as the source of truth.
* Choosing a data warehouse for operational application traffic.
* Assuming flexible schema automatically means NoSQL.
* Ignoring access patterns.
* Naming a storage category without explaining why.
* Discussing strengths without acknowledging trade-offs.

---

# ✅ Self Review

Before moving to the next playbook, confirm:

* ☐ I identified the major data types before choosing storage.
* ☐ I described important data characteristics.
* ☐ I considered access patterns and required guarantees.
* ☐ I chose storage categories before specific technologies.
* ☐ I separated large binary objects from metadata when appropriate.
* ☐ I identified the source of truth.
* ☐ I distinguished persistent storage from caching.
* ☐ I explained one trade-off for major storage decisions.
* ☐ I can justify why a system may need multiple storage technologies.

---

# 🎯 Key Lessons

* Start with the data, not the database.
* Different data types often require different storage categories.
* Data characteristics alone are not enough—access patterns and guarantees matter.
* Storage decisions should follow **Observation → Requirement → Decision → Trade-off**.
* A cache is an optimization, not automatically the source of truth.
* Multiple storage technologies are normal when each solves a distinct problem.

---

## Next Steps

* Review your answers against the sample answer key.
* Repeat Exercise 4 for a different system.
* Review the Storage Decision Tree Cheat Sheet.
* Review the Modern Storage Technologies Cheat Sheet.
* Continue to the next playbook.
