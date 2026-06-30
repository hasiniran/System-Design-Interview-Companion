# Capacity Estimation

> Capacity estimation isn't about getting the exact numbers.
>
> It's about understanding the **order of magnitude** so you can choose the right architecture.

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 15 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Interview Expectation** | Estimate • Explain • Apply |
| **Prerequisites** | Playbook 02 – Functional vs. Non-Functional Requirements |

---

## 📌 What Problem Does This Solve?

A common mistake in system design interviews is jumping directly into architecture.

Experienced engineers estimate the scale first.

Questions like these shape your design:

- How many users?
- How much traffic?
- How much storage?
- How much bandwidth?
- Is the system read-heavy or write-heavy?

You don't need perfect numbers.

You need estimates that are reasonable enough to guide architectural decisions.

---

# 🧠 Mental Model

Capacity estimation answers one question:

> **How big is the problem we're solving?**

The goal isn't mathematical precision.

The goal is choosing an architecture that fits the scale.

```text
Tiny Scale
      │
      ▼
Single Database

---------------------

Medium Scale
      │
      ▼
Distributed Database

---------------------

Massive Scale
      │
      ▼
Object Storage + Caching + CDN
```

Different scales require different architectures.

---

# 🏗 Interview Workflow

Capacity estimation comes after understanding the requirements.

```text
Understand the Product
        │
        ▼
Requirements
        │
        ▼
Product Characteristics
        │
        ▼
Capacity Estimation
        │
        ▼
Architecture
```

Never estimate before understanding the product.

---

# Product Characteristics

Before calculating numbers, identify how the system is expected to be used.

These are not requirements.

They are observations that influence both your estimates and your architecture.

| Characteristic | Why It Matters |
|----------------|----------------|
| Read-heavy vs. Write-heavy | Determines caching strategy |
| Large vs. Small Objects | Determines storage solution |
| Global vs. Regional Users | May require CDNs and multi-region deployments |
| Real-time vs. Asynchronous | Influences communication patterns |
| Predictable vs. Bursty Traffic | Impacts autoscaling and load balancing |

### Examples

**Twitter**

- Read-heavy
- Small objects
- Global users
- Bursty traffic

**Dropbox**

- Large objects
- Read-heavy
- Long-term storage
- High durability

---

# The Five Questions

Every estimation starts with these five questions.

1. How many users?
2. How often do they perform the action?
3. How large is each request or object?
4. How much data accumulates over time?
5. How much traffic does that generate?

If you can answer these, you can estimate almost any system.

---

# Quick Math Toolkit

Approximation is expected.

| Unit | Value |
|------|------:|
| 1 KB | 1,024 B |
| 1 MB | 1,024 KB |
| 1 GB | 1,024 MB |
| 1 TB | 1,024 GB |
| 1 PB | 1,024 TB |

Useful approximations:

| Value | Approximation |
|------|---------------|
| Million | 10⁶ |
| Billion | 10⁹ |
| Trillion | 10¹² |

Round numbers whenever possible.

Interviewers evaluate reasoning—not arithmetic.

---

# Interview Shortcuts

These formulas cover most interview scenarios.

### Average QPS

```text
Requests per Day
────────────────
     86,400
```

---

### Peak QPS

```text
≈ 5 × Average QPS
```

Use a different multiplier only if the interviewer specifies unusual traffic patterns.

---

### Storage

```text
Users
×
Objects per User
×
Average Object Size
```

---

### Bandwidth

```text
Peak QPS
×
Average Response Size
```

---

### Cache Memory

```text
Cached Objects
×
Average Object Size
```

---

# Worked Example

Suppose you're designing Dropbox.

Assumptions:

- 5 million daily active users
- 1 upload per user per day
- Average file size: 5 MB

Storage generated per day:

```text
5M × 1 × 5 MB

≈ 25 TB/day
```

Annual storage:

```text
25 TB × 365

≈ 9 PB/year
```

What do these numbers tell us?

- A relational database is not appropriate for storing files.
- Object storage is required.
- Metadata should be stored separately.
- Durability becomes a primary concern.

The numbers guide the architecture.

---

# From Numbers to Architecture

Capacity estimation isn't the final answer.

It's the bridge to architecture.

| Observation | Architectural Impact |
|-------------|----------------------|
| Millions of users | Horizontal scaling |
| Petabytes of storage | Object storage |
| Read-heavy | Caching |
| Global users | CDN |
| High write throughput | Queues |
| Large objects | Multipart upload |

Ask yourself:

> **What architectural decision does this estimate justify?**

---

# Common Mistakes

❌ Spending too much time on arithmetic.

❌ Forgetting to explain assumptions.

❌ Estimating numbers that never influence the design.

❌ Using unrealistic assumptions.

❌ Treating estimates as exact values.

---

## ⭐ Interview Insight

Interviewers rarely care whether your estimate is exactly right.

They care whether your reasoning is sound.

State your assumptions.

Show your calculations.

Explain how the estimates influence your design.

---

## ✅ Interview Checklist

Before moving to architecture:

- □ Did I identify the product characteristics?
- □ Did I estimate users?
- □ Did I estimate storage?
- □ Did I estimate traffic?
- □ Did I identify whether the system is read-heavy or write-heavy?
- □ Can I explain how these estimates affect my design?

---

## ⚠️ Interview Traps

❌ Assuming numbers without saying so.

❌ Spending ten minutes doing calculations.

❌ Optimizing for tiny traffic.

❌ Ignoring growth over time.

❌ Forgetting to connect estimates to architecture.

---

## ☕ Backend Java Lens

Capacity estimates influence implementation decisions.

| Observation | Possible Design Choice |
|-------------|------------------------|
| High read traffic | Redis caching |
| Large files | Object storage with metadata in SQL |
| High write throughput | Asynchronous processing with Kafka |
| Large user base | Stateless Spring Boot services behind a load balancer |
| Global traffic | CDN + regional deployments |

The architecture should always reflect the expected scale.

---

## 📝 Whiteboard Sketch

```text
Product

      │

      ▼

Characteristics

      │

      ▼

Capacity Estimates

      │

      ▼

Architecture Decisions
```

---

## 🎯 30-Second Recap

- ✔ Capacity estimation determines the scale of the system.
- ✔ Product characteristics guide your estimates.
- ✔ Approximate numbers are enough.
- ✔ Explain every assumption.
- ✔ Use estimates to justify architectural decisions.

---

## 💬 Practice Exercise

Your interviewer says:

> Design a cloud file storage service.

Without drawing any architecture:

1. Identify the product characteristics.
2. Estimate the number of users.
3. Estimate daily storage growth.
4. Estimate average and peak QPS.
5. Explain which architectural decisions these estimates influence.

---

## 💡 Key Takeaway

> **Capacity estimation is not a math exercise. It is the bridge between understanding the problem and designing the right architecture.**
