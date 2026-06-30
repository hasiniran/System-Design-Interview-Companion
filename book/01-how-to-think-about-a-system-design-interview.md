# How to Think About a System Design Interview

> Before learning technologies, learn how to think.

---

## 📌 What Problem Does This Solve?

Many candidates know technologies such as Redis, Kafka, PostgreSQL, and Kubernetes.

Yet they still struggle in system design interviews.

Why?

Because interviews evaluate **decision making**, not memorization.

The interviewer is trying to understand **how you think**.

---

## 🧠 Mental Model

System Design is simply this:

> Given a problem and a set of constraints, choose the simplest architecture that satisfies the requirements.

Everything else follows from this idea.

Good engineers optimize for trade-offs.

Not perfection.

---

## ⭐ Interview Insight

Your first 10 minutes matter more than your last 30.

Do **not** start drawing architecture immediately.

Start asking questions.

---

## Step 1 — Clarify Requirements

Always begin by understanding the problem.

Example:

> Design Instagram.

Ask questions like:

- Mobile or web?
- Photos only?
- Videos?
- Stories?
- Messaging?
- Search?
- Notifications?
- Public or private accounts?
- Expected users?

Every answer changes the design.

---

## Step 2 — Identify Functional Requirements

What should the system do?

Examples:

- Upload photos
- View feed
- Follow users
- Like posts
- Comment
- Search

---

## Step 3 — Identify Non-Functional Requirements

How well should the system perform?

Examples:

- Availability
- Latency
- Scalability
- Reliability
- Security
- Cost
- Consistency

Interviewers care about these more than most candidates realize.

---

## Step 4 — Estimate Scale

Estimate before designing.

Ask:

- Daily Active Users
- Requests per second
- Average object size
- Storage growth
- Read/write ratio

Your estimates don't have to be perfect.

They need to be reasonable.

---

## Step 5 — Draw a High-Level Architecture

Now start drawing.

Keep it simple.

```text
Users
   │
Load Balancer
   │
Application
   │
Database
```

Only introduce additional components when there is a reason.

---

## Step 6 — Find Bottlenecks

Ask yourself:

- What breaks first?
- What becomes slow?
- What becomes expensive?
- What becomes unavailable?

Every bottleneck should lead to a design improvement.

---

## Step 7 — Discuss Trade-offs

This is where senior candidates stand out.

Instead of saying

> Use Redis.

Explain

> I would introduce Redis because reads greatly outnumber writes. This reduces database load and improves latency, at the cost of additional complexity and cache invalidation.

---

## ⚠️ Interview Traps

❌ Starting with architecture

❌ Overengineering

❌ Never discussing trade-offs

❌ Ignoring scale

❌ Forgetting failure scenarios

❌ Saying "microservices" without justification

---

## ☕ Backend Java Lens

Think in Spring Boot terms.

Can this service be:

- Stateless?
- Horizontally scaled?
- Behind a load balancer?
- Connected through asynchronous messaging?

These questions naturally guide better designs.

---

## ☁️ AWS Mapping

| Concept | AWS |
|----------|-----|
| DNS | Route 53 |
| Load Balancer | ALB |
| Compute | EC2 / ECS / EKS |
| Cache | ElastiCache |
| Database | RDS |
| Object Storage | S3 |
| Queue | SQS |

---

## 🎯 30-Second Recap

Every interview follows the same pattern.

1. Clarify requirements.
2. Identify constraints.
3. Estimate scale.
4. Design the simplest solution.
5. Find bottlenecks.
6. Improve the design.
7. Explain trade-offs.

Don't optimize for the perfect architecture.

Optimize for a clear thought process.

---

## 💬 Practice

Your interviewer says:

> Design Dropbox.

Spend two minutes writing only clarifying questions.

Do not draw anything.
