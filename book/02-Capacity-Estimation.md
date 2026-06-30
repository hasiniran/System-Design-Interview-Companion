# Capacity Estimation

> Good estimates don't have to be exact. They need to be reasonable enough to drive architectural decisions.

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 10 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Interview Expectation** | Design |
| **Prerequisites** | Playbook 01 – How to Think About a System Design Interview<br>Playbook 02 – Functional vs. Non-Functional Requirements |

---

## 📌 What Problem Does This Solve?

Before choosing databases, caches, or messaging systems, you need to understand the scale of the problem.

A design for 1,000 users is very different from a design for 100 million users.

Capacity estimation helps answer questions like:

- Do we need caching?
- Can a single database handle the traffic?
- How much storage is required?
- Is horizontal scaling necessary?
- What are the likely bottlenecks?

The goal isn't perfect accuracy.

The goal is making informed engineering decisions.

---

## 🧠 Mental Model

Think of capacity estimation as creating a rough blueprint before constructing a building.

You don't need every measurement.

You need enough information to make good design decisions.

A reasonable estimate is far more valuable than no estimate at all.

---

# 🏗️ Estimation Drives Design

```text
Requirements
      │
      ▼
Estimate Scale
      │
      ▼
Identify Bottlenecks
      │
      ▼
Choose Architecture
```

Never estimate for the sake of math.

Estimate because every number influences your design.

---

# What Should You Estimate?

Most interview questions can be answered by estimating a few key values.

### Users

- Daily Active Users (DAU)
- Monthly Active Users (MAU)

Example:

10 million daily users.

---

### Traffic

Estimate:

- Requests per second (RPS)
- Peak Requests per second

Example:

```
100 million requests/day

↓

≈1,200 requests/sec

↓

Peak ≈2–5× average

≈3,000–6,000 requests/sec
```

Peak traffic is often more important than average traffic.

---

### Read vs Write Ratio

Most systems receive far more reads than writes.

Examples:

| System | Read : Write |
|---------|--------------|
| Social Feed | 100 : 1 |
| News Website | 500 : 1 |
| Banking | 2 : 1 |
| Chat Application | 5 : 1 |

This ratio strongly influences caching decisions.

---

### Storage

Estimate:

```
Average object size

×

Objects per day

×

Retention period
```

Example:

```
1 MB/photo

×

5 million uploads/day

=

5 TB/day
```

Now you know storage is a major concern.

---

### Bandwidth

Estimate:

```
Response Size

×

Requests per Second
```

Large media files require significantly more bandwidth than JSON APIs.

---

## The Numbers Don't Need to Be Perfect

Interviewers know you're making assumptions.

State them clearly.

Example:

> "I'll assume 10 million daily active users to keep the calculations simple."

Making reasonable assumptions is a strength—not a weakness.

---

# Quick Reference

## Common Conversions

```
1 KB ≈ 1,000 bytes

1 MB ≈ 1,000 KB

1 GB ≈ 1,000 MB

1 TB ≈ 1,000 GB
```

---

## Time

```
1 minute = 60 seconds

1 hour = 3,600 seconds

1 day = 86,400 seconds
```

---

## Average RPS

```
Requests Per Day

──────────────

86,400
```

---

## Peak RPS

A common interview assumption:

```
Peak

≈ 2–5 × Average RPS
```

If the interviewer doesn't specify, mention your assumption.

---

# Example

Design a photo-sharing application.

Assume:

- 20 million DAU
- 5 photos uploaded per second
- Average photo size = 2 MB

Estimate storage.

```
5 uploads/sec

×

86,400 sec/day

=

432,000 uploads/day

×

2 MB

=

864 GB/day

≈315 TB/year
```

Immediately, you know:

- Object storage is required.
- Backups matter.
- Compression becomes valuable.

Notice how the estimate leads directly to architecture.

---

## ⭐ Interview Insight

Don't spend ten minutes doing arithmetic.

Spend one minute estimating.

Spend the remaining time explaining how those estimates influence your design.

The interviewer cares more about your reasoning than your calculator skills.

---

## ⚠️ Interview Traps

❌ Trying to calculate exact numbers.

❌ Forgetting peak traffic.

❌ Ignoring storage growth.

❌ Never stating assumptions.

❌ Spending too much interview time on calculations.

---

## ☕ Backend Java Lens

Capacity estimates influence backend implementation.

Examples:

| Estimate | Possible Design Choice |
|----------|------------------------|
| High read traffic | Redis cache |
| Large media storage | Store metadata in a database, files in object storage |
| High write throughput | Asynchronous processing with a message queue |
| Millions of users | Stateless Spring Boot services behind a load balancer |

Capacity planning drives architecture—not the framework.

---

## 📝 Whiteboard Sketch

```text
      Users
        │
        ▼

 Estimate Traffic

        │
        ▼

 Estimate Storage

        │
        ▼

 Identify Bottlenecks

        │
        ▼

 Design Architecture
```

---

## 🎯 30-Second Recap

- ✔ Estimate before designing.
- ✔ State your assumptions.
- ✔ Estimate users, traffic, storage, and bandwidth.
- ✔ Focus on peak traffic, not just average.
- ✔ Let estimates guide architectural decisions.
- ✔ Good estimates are reasonable—not perfect.

---

## 💬 Practice Exercise

Your interviewer asks:

> Design YouTube.

Assume:

- 50 million daily active users.
- 10 million videos uploaded each day.
- Average video size: 200 MB.

Estimate:

1. Daily storage growth.
2. Peak upload traffic.
3. Which components become bottlenecks first.
4. How those estimates influence your architecture.

---

## 💡 Key Takeaway

> **Capacity estimation isn't about doing math—it's about understanding scale well enough to make good engineering decisions.**
