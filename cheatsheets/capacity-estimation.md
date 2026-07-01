# Capacity Estimation Cheat Sheet

> Capacity estimation isn't about getting exact numbers—it's about estimating the **order of magnitude** to justify architectural decisions.

---

# 🚀 Interview Workflow

```text
Understand Product
        │
        ▼
Requirements
        │
        ▼
Product Characteristics
        │
        ▼
Assumptions
        │
        ▼
Capacity Estimation
        │
        ▼
Architecture Decisions
```

Never estimate before understanding the product.

---

# ✅ Step 1 — Identify Product Characteristics

Before calculating anything, understand the workload.

| Characteristic | Architecture Hint |
|----------------|-------------------|
| Read-heavy | Cache, CDN |
| Write-heavy | Queue, Partitioning |
| Large Objects | Object Storage |
| Small Objects | Database |
| Global Users | CDN, Multi-region |
| Regional Users | Single Region |
| Real-time | WebSockets, Streaming |
| Asynchronous | Queue |
| Bursty Traffic | Autoscaling |
| Predictable Traffic | Scheduled Scaling |

---

# ✅ Step 2 — Make Assumptions

If the interviewer doesn't specify values, state reasonable assumptions.

Typical assumptions:

| Metric | Example |
|--------|---------|
| Registered Users | 100M – 1B |
| Daily Active Users | 10–20% of users |
| Requests / User / Day | 5–100 |
| Writes / User / Day | Product dependent |
| Average Object Size | Product dependent |
| Data Retention | Months or Years |

> **Interview Tip**
>
> Clearly state your assumptions before calculating.

---

# ✅ Step 3 — Estimate Capacity

## Storage

```
Users × Objects × Size
```

Annual storage

```
Storage/day × 365
```

---

## Average QPS

```
Requests/day ÷ 86,400
```

---

## Peak QPS

```
Average QPS × (3–5)
```

Use **3×** for predictable traffic.

Use **5×** for consumer applications or unknown traffic patterns.

---

## Bandwidth

```
Peak QPS × Response Size
```

Estimate only if it influences the architecture.

---

# ✅ Step 4 — Translate Estimates into Decisions

Every estimate should answer:

> **What architectural decision does this force me to make?**

Examples:

| Observation | Decision |
|-------------|----------|
| Large binary files | Object Storage |
| Storage grows to PB scale | Separate metadata from file storage |
| Read-heavy workload | Cache and CDN |
| Write-heavy workload | Queue |
| Global users | CDN / Multi-region |
| High Peak QPS | Load Balancer + Horizontal Scaling |
| High durability | Replication |
| Frequently accessed data | Cache |

---

# 🎤 Interview Language

Use this pattern throughout the interview.

> **Estimate → Decision → Technology**

Example:

> Storage is approximately **36 PB/year**.

↓

> The system will store binary objects at petabyte scale.

↓

> I'll store metadata in a relational database and file contents in object storage.

---

Another example:

> Peak traffic is approximately **5,000 QPS**.

↓

> A single application instance is unlikely to handle this load.

↓

> I'll deploy multiple stateless application servers behind a load balancer.

---

# ⚡ Capacity Estimation Checklist

Before moving to architecture, ask yourself:

- □ Did I understand the product?
- □ Did I identify the workload?
- □ Did I state assumptions?
- □ Did I estimate storage?
- □ Did I estimate traffic?
- □ Did I estimate Peak QPS?
- □ Did every estimate influence a design decision?

---

# ⚠️ Common Mistakes

❌ Calculating before understanding the product.

❌ Treating assumptions as facts.

❌ Using unrealistic numbers.

❌ Spending too much time on arithmetic.

❌ Estimating values that never influence the architecture.

❌ Choosing technologies without explaining why.

---

# 💡 Quick Reference

```text
Storage
Users × Objects × Size

↓

Traffic
Requests/day ÷ 86,400

↓

Peak Traffic
Average QPS × (3–5)

↓

Architecture
Explain WHY every estimate changes your design
```

---

# 🎯 Remember

Capacity estimation is **not** a math exercise.

Interviewers are evaluating whether you can:

1. Make reasonable assumptions.
2. Estimate the order of magnitude.
3. Connect estimates to architectural decisions.
4. Explain your reasoning clearly.

If a calculation doesn't influence your architecture, you probably didn't need to calculate it.
