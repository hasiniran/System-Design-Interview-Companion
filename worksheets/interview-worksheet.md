# System Design Interview Worksheet

> Use this worksheet to structure a system design problem from clarification to architectural decisions.

---

## 📖 How to Use This Worksheet

Do not try to complete every section mechanically.

Use the sections that influence the architecture.

The goal is to move through the interview with a clear reasoning chain:

```text
Understand Product
        │
        ▼
Clarify Scope
        │
        ▼
Requirements
        │
        ▼
Workload Characteristics
        │
        ▼
Assumptions
        │
        ▼
Capacity Estimation
        │
        ▼
Understand the Data
        │
        ▼
Storage Design
        │
        ▼
Architecture Decisions
        │
        ▼
Trade-offs
```

---

# 1. Product Summary

Describe the system in one or two sentences.

```text
____________________________________________________

____________________________________________________
```

---

# 2. Clarifying Questions

Ask only questions whose answers could influence the design.

Examples:

* What is in scope?
* How many users?
* What is the maximum object size?
* Is real-time behavior required?
* How long is data retained?
* Is strong consistency required?

```text
1. __________________________________________________

2. __________________________________________________

3. __________________________________________________

4. __________________________________________________

5. __________________________________________________
```

---

# 3. Functional Requirements

What should users be able to do?

Group related capabilities where useful.

```text
1. __________________________________________________

2. __________________________________________________

3. __________________________________________________

4. __________________________________________________

5. __________________________________________________
```

---

# 4. Non-Functional Requirements

How well should the system behave?

Examples:

* Availability
* Reliability
* Durability
* Latency
* Consistency
* Security
* Scalability

```text
1. __________________________________________________

2. __________________________________________________

3. __________________________________________________

4. __________________________________________________

5. __________________________________________________
```

---

## Top 3 Non-Functional Requirements

Prioritize the qualities that most strongly influence the architecture.

| Priority | Requirement | Why It Matters |
| -------- | ----------- | -------------- |
| 1        |             |                |
| 2        |             |                |
| 3        |             |                |

---

# 5. Workload Characteristics

Describe how the system behaves.

Consider:

* Read-heavy vs write-heavy
* Large vs small objects
* Global vs regional users
* Real-time vs asynchronous
* Predictable vs bursty traffic
* Long-term vs short-term retention

| Characteristic | Observation | Architectural Implication |
| -------------- | ----------- | ------------------------- |
|                |             |                           |
|                |             |                           |
|                |             |                           |
|                |             |                           |
|                |             |                           |

---

# 6. Assumptions

State reasonable assumptions before calculating.

| Assumption            | Value |
| --------------------- | ----- |
| Registered Users      |       |
| Daily Active Users    |       |
| Requests / User / Day |       |
| Writes / User / Day   |       |
| Average Object Size   |       |
| Retention Period      |       |

Additional assumptions:

```text
____________________________________________________

____________________________________________________
```

---

# 7. Capacity Estimation

Estimate only what influences the architecture.

---

## Average QPS

```text
Requests/day ÷ 86,400

____________________________________________________
```

### Architecture Implication

```text
____________________________________________________

____________________________________________________
```

---

## Peak QPS

```text
Peak multiplier × Average QPS

____________________________________________________
```

Peak multiplier assumption:

```text
____________________________________________________
```

### Architecture Implication

```text
____________________________________________________

____________________________________________________
```

---

## Storage

```text
Users × Objects × Average Object Size

____________________________________________________
```

Daily growth:

```text
____________________________________________________
```

Annual growth:

```text
____________________________________________________
```

### Architecture Implication

```text
____________________________________________________

____________________________________________________
```

---

## Bandwidth — Optional

```text
Peak QPS × Average Response Size

____________________________________________________
```

### Architecture Implication

```text
____________________________________________________

____________________________________________________
```

---

## Biggest Capacity Constraint

Choose the dominant constraint:

* Storage
* Peak QPS
* Bandwidth
* Global latency
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

# 8. Storage Design

Do not choose a database first.

Begin by identifying the actual data the system stores.

---

## Major Data Types

List actual data nouns.

Examples:

* Users
* Orders
* Files
* Metadata
* Permissions
* Messages
* Locations
* Comments

| Data Type | Example |
| --------- | ------- |
|           |         |
|           |         |
|           |         |
|           |         |
|           |         |
|           |         |

---

## Data → Requirement → Storage Decision

For each major data type:

1. Identify its important data characteristics.
2. Identify the dominant access pattern or required guarantee.
3. Choose a storage category.
4. Explain the trade-off.

| Data | Data Characteristics | Access Pattern / Guarantee | Storage Category | Trade-off |
| ---- | -------------------- | -------------------------- | ---------------- | --------- |
|      |                      |                            |                  |           |
|      |                      |                            |                  |           |
|      |                      |                            |                  |           |
|      |                      |                            |                  |           |
|      |                      |                            |                  |           |
|      |                      |                            |                  |           |

---

## Source of Truth

Which data must remain authoritative and durable?

```text
____________________________________________________

____________________________________________________
```

Why?

```text
____________________________________________________

____________________________________________________
```

---

## Derived / Rebuildable Data

Which data can be reconstructed from authoritative data?

Examples:

* Search indexes
* Cached feeds
* Aggregated counters
* Materialized views

```text
____________________________________________________

____________________________________________________
```

---

## Cache Candidates

Which data is:

* Frequently accessed?
* Expensive to recompute?
* Safe to temporarily duplicate?

```text
____________________________________________________

____________________________________________________
```

Why is caching appropriate?

```text
____________________________________________________

____________________________________________________
```

---

## Biggest Storage Constraint

Choose the dominant constraint:

* Storage scale
* Transactional consistency
* Read latency
* Write throughput
* Query flexibility
* Relationship traversal
* Search
* Retention
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

# 9. High-Level Architecture

Draw the major components and request flow.

```text
Client
  │
  ▼
____________________
  │
  ▼
____________________
  │
  ├──────────────► ____________________
  │
  └──────────────► ____________________
```

Questions to consider:

* Where does traffic enter?
* Which services are stateless?
* Where is authoritative data stored?
* Which work happens asynchronously?
* Where are caches introduced?
* Which components can fail independently?

---

# 10. Architecture Decisions

For each major decision, connect the observation to the requirement and trade-off.

| Observation | Requirement | Decision | Why? | Trade-off |
| ----------- | ----------- | -------- | ---- | --------- |
|             |             |          |      |           |
|             |             |          |      |           |
|             |             |          |      |           |
|             |             |          |      |           |
|             |             |          |      |           |

---

# 11. Biggest System Constraint

What is the dominant architectural challenge?

Examples:

* Storage scale
* Peak traffic
* Global latency
* Write throughput
* Strong consistency
* Availability
* Search
* Real-time communication

```text
____________________________________________________
```

Why?

```text
____________________________________________________

____________________________________________________
```

What does this constraint force you to build?

```text
____________________________________________________

____________________________________________________
```

---

# 12. Trade-offs

Identify the most important compromises in the design.

| Decision | Benefit | Trade-off |
| -------- | ------- | --------- |
|          |         |           |
|          |         |           |
|          |         |           |

---

# 13. Interview Summary

Summarize the design in 30–60 seconds.

```text
The primary constraint is ___________________________.

Because the workload is ____________________________,
I chose ____________________________________________.

The major storage decisions are ____________________
____________________________________________________.

The main trade-off is ______________________________
____________________________________________________.
```

---

# 14. Self Review

Before finishing, check:

* ☐ I clarified the scope before designing.
* ☐ I separated functional and non-functional requirements.
* ☐ I prioritized the important non-functional requirements.
* ☐ I identified workload characteristics.
* ☐ I stated assumptions before estimating.
* ☐ I estimated only numbers that influence architecture.
* ☐ I identified the biggest capacity constraint.
* ☐ I listed actual data types before choosing storage.
* ☐ I separated data characteristics from access patterns and guarantees.
* ☐ I identified authoritative source-of-truth data.
* ☐ I identified derived or rebuildable data.
* ☐ I identified appropriate cache candidates.
* ☐ I justified major storage decisions.
* ☐ I identified the biggest system constraint.
* ☐ I explained important trade-offs.
* ☐ I can summarize the design clearly.

---

# 🎯 Core Reasoning Pattern

Use this throughout the interview:

```text
Observation
    │
    ▼
Requirement
    │
    ▼
Decision
    │
    ▼
Trade-off
```

Example:

```text
Photos are large binary objects
        │
        ▼
Need massively scalable durable storage
        │
        ▼
Choose Object Storage
        │
        ▼
Accept limited querying and maintain metadata separately
```

---

# 💡 Key Takeaway

> A strong system design interview is not a list of technologies.
>
> It is a chain of justified decisions.
