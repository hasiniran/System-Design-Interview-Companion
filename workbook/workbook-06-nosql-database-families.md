# Workbook 06 – NoSQL Database Families

> Practice choosing and modeling NoSQL databases without repeating the full system-design workflow.

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 20–25 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Focus** | Database Family • Access Patterns • Partition Keys • Trade-offs |
| **Prerequisites** | Playbook 06 – NoSQL Database Families |
| **Recommended Format** | Short written answers + brief verbal explanation |

---

## How to Use This Workbook

Do **not** repeat:

- Full functional requirements
- Capacity estimation
- Full architecture
- Detailed relational schema design

Focus only on the new NoSQL decisions:

```text
Access Pattern
      ↓
Required Guarantee
      ↓
Database Family
      ↓
Primary / Partition Key
      ↓
Trade-off
```

Keep most answers to one or two sentences.

---

# Exercise 1 – Choose the Database Family

For each scenario:

1. Identify the dominant access pattern.
2. Choose the best database family.
3. State one trade-off.

Possible choices:

- Relational
- Document
- Key-value
- Wide-column
- Graph

---

## Scenario 1 – User Sessions

Sessions are retrieved only by `session_id`.

Sessions expire automatically after a short period.

```text
Access pattern:

Database family:

Why:

Trade-off:
```

---

## Scenario 2 – Product Catalog

Products have different attributes depending on category.

Example:

```text
Laptop:
CPU, RAM, storage

Book:
author, ISBN, pages

Shirt:
size, color, material
```

Products are usually retrieved as complete records.

```text
Access pattern:

Database family:

Why:

Trade-off:
```

---

## Scenario 3 – IoT Events

Millions of devices continuously generate events.

Queries usually retrieve:

```text
All events for device_id
between time A and time B
```

```text
Access pattern:

Database family:

Why:

Trade-off:
```

---

## Scenario 4 – Friend Recommendations

The system frequently asks:

```text
Who are the friends-of-friends of this user?
```

and performs other multi-hop relationship queries.

```text
Access pattern:

Database family:

Why:

Trade-off:
```

---

## Scenario 5 – Banking Transfers

The system must:

- Transfer money between accounts
- Prevent negative balances
- Guarantee that debit and credit succeed or fail together
- Support reporting and reconciliation

```text
Access pattern:

Database family:

Why:

Trade-off:
```

---

## Scenario 6 – Messaging History

The system stores a very large number of messages.

The common query is:

```text
Get the latest 50 messages
for conversation_id
ordered by timestamp
```

```text
Access pattern:

Database family:

Why:

Trade-off:
```

---

# Exercise 2 – Access-Pattern-First Modeling

For each scenario, define the key that best supports the dominant query.

---

## 2.1 User Sessions

Query:

```text
Get session by session_id
```

```text
Primary / partition key:

Why:
```

---

## 2.2 Messaging History

Query:

```text
Get messages for conversation_id
ordered by timestamp
```

```text
Partition key:

Clustering / sort key:

Why:
```

---

## 2.3 IoT Events

Query:

```text
Get device events
for one device
within a time range
```

```text
Partition key:

Clustering / sort key:

Why:
```

---

## 2.4 Product Documents

Query:

```text
Get product by product_id
```

```text
Document key:

What data would you embed?

What data would you reference instead?

Why:
```

---

# Exercise 3 – SQL or NoSQL?

For each case:

1. Choose relational or NoSQL.
2. If NoSQL, name the family.
3. State the deciding factor.

---

## Scenario 1

A customer profile contains:

- Name
- Preferences
- Notification settings
- UI settings

The profile is almost always read and updated as one object.

```text
Decision:

Reason:
```

---

## Scenario 2

Orders must support:

- Query by customer
- Query by status
- Query by date
- Payment reconciliation
- Transactional updates

```text
Decision:

Reason:
```

---

## Scenario 3

Feature flags are retrieved by:

```text
user_id + feature_name
```

Millions of lookups occur per second.

```text
Decision:

Reason:
```

---

## Scenario 4

Product descriptions have flexible attributes, but inventory must never become negative.

```text
Product descriptions:

Inventory:

Why use different stores:
```

---

## Scenario 5

A social app stores follower relationships.

Most queries are simple:

```text
Get all followers of user_id
```

There are very few multi-hop traversals.

```text
Decision:

Reason:
```

---

# Exercise 4 – Partition-Key Judgment

For each proposed key:

1. Decide whether it is good or risky.
2. Explain why.
3. Suggest a better key if needed.

---

## 4.1 IoT Events

```text
partition_key = date
```

Most traffic is for today's events.

```text
Good or risky:

Why:

Better key:
```

---

## 4.2 Messaging

```text
partition_key = conversation_id
```

Most conversations are small, but a few large public channels receive extremely high traffic.

```text
Good or risky:

Why:

Possible mitigation:
```

---

## 4.3 User Events

```text
partition_key = user_id
```

Most users generate little traffic, but a few celebrity accounts receive huge read and write traffic.

```text
Good or risky:

Why:

Possible mitigation:
```

---

## 4.4 Orders

```text
partition_key = order_status
```

Possible values:

```text
PENDING
PAID
SHIPPED
CANCELLED
```

```text
Good or risky:

Why:

Better key:
```

---

# Exercise 5 – Source of Truth

A system uses:

```text
Relational DB → Orders and payments
Document DB   → Product catalog
Search index  → Product search
Cache         → Popular product pages
```

For each store, classify it.

| Store | Source of Truth or Derived? | What Happens If It Is Stale? | Can It Be Rebuilt? |
|---|---|---|---|
| Relational DB | | | |
| Document DB | | | |
| Search index | | | |
| Cache | | | |

---

## 5.1 Ownership Question

Why is it dangerous for two databases to both appear to be the authoritative owner of the same field?

```text
Answer:
```

---

# Exercise 6 – Two-Minute Interview Response

## Scenario: Product Catalog

Requirements:

- Products have category-specific attributes.
- Product pages are read frequently.
- Keyword and faceted search is required.
- Inventory must never become negative.
- Price updates must remain correct.
- Search results may lag behind the source of truth by a few seconds.

Do not redesign the entire system.

Cover only the data-layer decision:

1. Which data stays relational?
2. Which data may use a document database?
3. Why?
4. What is the source of truth?
5. What would the primary key be?
6. How would search be handled?
7. What consistency can be relaxed?
8. One important trade-off

```text
Relational data:

Document data:

Why:

Source of truth:

Primary / document key:

Search:

Consistency:

Trade-off:
```

---

# Rapid Recall – 5 Minutes

Answer each in one sentence.

## 1. What is the biggest mistake when choosing NoSQL?

```text
Answer:
```

## 2. What should come before choosing a database family?

```text
Answer:
```

## 3. When does a document database fit well?

```text
Answer:
```

## 4. When does a key-value store fit well?

```text
Answer:
```

## 5. When does a wide-column database fit well?

```text
Answer:
```

## 6. When does a graph database fit well?

```text
Answer:
```

## 7. What makes a good partition key?

```text
Answer:
```

## 8. Why is denormalization common in NoSQL systems?

```text
Answer:
```

## 9. Why does NoSQL not automatically mean eventual consistency?

```text
Answer:
```

## 10. Why must the source of truth be explicit?

```text
Answer:
```

---

# Completion Check

You are ready to move to Playbook 07 when you can explain:

- Why access patterns come before database choice
- The difference between document, key-value, wide-column, and graph databases
- When relational storage is still the better choice
- How to choose a partition key
- Why hot partitions happen
- Why NoSQL often duplicates data
- Which store is authoritative versus derived
- What trade-off comes with each NoSQL family

You do not need to memorize specific products.

The goal is to choose the right **database family** and justify the decision.

---

## Final Reflection

Which NoSQL family still feels least automatic?

```text
Answer:
```

What is the most important rule you want to remember in an interview?

```text
Answer:
```
