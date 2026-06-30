# How to Think About a System Design Interview

> Before learning technologies, learn how to think.

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 8 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Interview Expectation** | Design |
| **Prerequisites** | None |

---

## 📌 What Problem Does This Solve?

Many candidates know technologies like Redis, Kafka, PostgreSQL, Kubernetes, and AWS services.

Yet they still struggle in system design interviews.

Why?

Because interviewers aren't evaluating how many technologies you know.

They're evaluating **how you approach an unfamiliar problem**.

A strong interview isn't about producing the "perfect" architecture—it's about demonstrating structured thinking, making reasonable assumptions, and explaining trade-offs.

This topic introduces a repeatable framework you can apply to almost any backend system design interview.

---

## 🧠 Mental Model

System design can be summarized in one sentence:

> **Given a problem and a set of constraints, design the simplest system that satisfies the requirements.**

Everything you add to a system should solve a specific problem.

If a component doesn't solve a problem, it probably doesn't belong in your design.

---

# 🧭 The 7-Step Interview Framework

Almost every backend system design interview follows this flow.

```text
1. Clarify Requirements
          │
          ▼
2. Estimate Scale
          │
          ▼
3. High-Level Architecture
          │
          ▼
4. Deep Dive
          │
          ▼
5. Identify Bottlenecks
          │
          ▼
6. Scale the Design
          │
          ▼
7. Discuss Trade-offs
```

**Memorize this sequence.**

Whenever you're unsure what to do next during an interview, return to this framework.

---

## Step 1 — Clarify Requirements

Never begin by drawing architecture.

Instead, understand the problem.

Example:

> **Design Instagram.**

Clarify questions such as:

- Is this mobile, web, or both?
- Photos only or videos too?
- Public and private accounts?
- Stories?
- Messaging?
- Search?
- Notifications?
- Expected number of users?

Every answer changes the design.

---

## Step 2 — Estimate Scale

Before choosing technologies, estimate the size of the system.

Useful questions:

- Daily active users?
- Requests per second?
- Read-to-write ratio?
- Average object size?
- Storage growth?
- Geographic regions?

Your estimates don't need to be exact.

Interviewers are evaluating your reasoning, not your arithmetic.

---

## Step 3 — Design the High-Level Architecture

Now draw the simplest possible solution.

```text
        Users
          │
          ▼
   Load Balancer
          │
          ▼
 Application Servers
          │
          ▼
      Database
```

Keep the first version intentionally simple.

Only add complexity when you identify a problem that requires it.

---

## Step 4 — Deep Dive

Choose one important component and explain it in detail.

Examples:

- Database selection
- Caching
- Authentication
- Feed generation
- Search
- Storage

The interviewer usually decides where to go deeper.

---

## Step 5 — Identify Bottlenecks

Ask yourself:

- What becomes slow?
- What becomes expensive?
- What becomes unavailable?
- What fails under high traffic?

Every bottleneck should naturally lead to an improvement.

Example:

High database load

↓

Introduce caching.

---

## Step 6 — Scale the Design

Now improve the system.

Possible improvements:

- Horizontal scaling
- Load balancing
- Caching
- Replication
- Sharding
- Asynchronous messaging
- CDN
- Rate limiting

Don't introduce technologies because they're popular.

Introduce them because they solve a specific bottleneck.

---

## Step 7 — Discuss Trade-offs

This is where experienced engineers stand out.

Instead of saying:

> "I'd use Redis."

Explain:

> "Since reads greatly outnumber writes, I'd introduce Redis to reduce database load and improve latency. The trade-off is additional operational complexity and cache invalidation."

Interviewers care more about your reasoning than your final answer.

---

## 📊 What Interviewers Are Evaluating

| Stage | What they're evaluating |
|--------|-------------------------|
| Clarifying Questions | Can you define the problem? |
| Estimation | Can you reason with incomplete information? |
| High-Level Design | Can you decompose complexity? |
| Deep Dive | Do you understand your chosen technologies? |
| Bottlenecks | Can you identify weaknesses? |
| Trade-offs | Can you justify engineering decisions? |
| Communication | Can you explain clearly and logically? |

---

## 🆘 If You Get Stuck

Don't panic.

Return to one of these questions:

- Where is the bottleneck?
- What receives the most traffic?
- Where is the data stored?
- Can this be cached?
- Can this be processed asynchronously?
- Can this component be scaled horizontally?

These questions almost always move the discussion forward.

---

## ⏱️ Suggested Timeline

For a typical **45-minute interview**:

| Activity | Time |
|----------|------|
| Clarify Requirements | 5 min |
| Estimate Scale | 5 min |
| High-Level Design | 10 min |
| Deep Dive | 15 min |
| Scaling & Bottlenecks | 5 min |
| Trade-offs & Wrap-up | 5 min |

Adjust naturally based on the interviewer's guidance.

---

## ⭐ Interview Insight

The first architecture you draw is **not** expected to be the final design.

Strong candidates build the system incrementally.

Start simple.

Improve it as new constraints emerge.

This mirrors how real systems evolve.

---

## ⚠️ Interview Traps

- ❌ Jumping straight into architecture.
- ❌ Overengineering the first design.
- ❌ Never estimating scale.
- ❌ Ignoring trade-offs.
- ❌ Using buzzwords without justification.
- ❌ Forgetting to discuss failure scenarios.

---

## ☕ Backend Java Lens

When designing backend services, think in terms of stateless Spring Boot applications.

Ask yourself:

- Can this service be stateless?
- Can multiple instances run behind a load balancer?
- Should this operation be asynchronous?
- Where should state be stored?

These questions naturally lead to scalable architectures.

---

## 📝 Whiteboard Sketch

```text
                Users
                  │
                  ▼
         Load Balancer
                  │
        ┌─────────┴─────────┐
        ▼                   ▼
   Application         Application
      Server              Server
        │                   │
        └─────────┬─────────┘
                  ▼
             Database
```

This is enough to begin almost any interview.

---

## 🎯 30-Second Recap

- ✔ Ask questions before designing.
- ✔ Estimate scale early.
- ✔ Start with a simple architecture.
- ✔ Improve the design one bottleneck at a time.
- ✔ Explain every design decision.
- ✔ Discuss trade-offs openly.
- ✔ Interviewers evaluate your thinking more than your final diagram.

---

## 💬 Practice Exercise

Your interviewer says:

> **Design Dropbox.**

Without drawing anything, spend two minutes answering:

1. What clarifying questions would you ask?
2. What assumptions would you make?
3. What would your first high-level architecture look like?

---

## 💡 Key Takeaway

> **System design interviews are not about knowing every technology—they're about making thoughtful engineering decisions under constraints and communicating them clearly.**
