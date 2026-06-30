# Functional vs. Non-Functional Requirements

> The first few minutes of a system design interview determine the direction of the entire discussion.

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 10 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Interview Expectation** | Requirements Discovery |
| **Prerequisites** | Playbook 01 – How to Think About a System Design Interview |

---

## 📌 What Problem Does This Solve?

Many candidates immediately begin drawing databases, caches, and load balancers.

Experienced engineers don't.

Before designing a system, they first answer two questions:

1. **What should the system do?** *(Functional Requirements)*
2. **How well should it do it?** *(Non-Functional Requirements)*

These answers determine almost every architectural decision that follows.

---

## 🧠 Mental Model

Think of requirements as answering two different questions.

### Functional Requirements

> **What capabilities should the system provide?**

### Non-Functional Requirements

> **How should the system behave while providing those capabilities?**

A system can satisfy every functional requirement and still fail because it doesn't meet the non-functional requirements.

---

## 🏗️ Requirements Drive Architecture

```text
Business Problem
        │
        ▼
Functional Requirements
        │
        ▼
Non-Functional Requirements
        │
        ▼
Architecture Decisions
```

Don't choose technologies first.

Choose them because they satisfy the requirements.

---

# Functional Requirements

Functional requirements describe **features**.

They define the capabilities the system must provide.

Ask yourself:

> **What should users be able to do?**

### Examples

For Instagram:

- Create an account
- Upload photos
- Follow users
- View feed
- Like posts
- Comment
- Search users

For Dropbox:

- Upload files
- Download files
- Share files
- Synchronize files
- View file history

A functional requirement is something a user can observe or interact with.

A useful question is:

> **"Can the user do this?"**

---

## Organize Before You Prioritize

Grouping related requirements makes your thought process easier to follow.

Example:

### File Management

- Upload
- Download
- Delete

### Sharing

- Share files
- Share folders
- Manage permissions

### Collaboration

- Version history
- Real-time editing

---

# Non-Functional Requirements

Non-functional requirements describe **system qualities**.

They answer:

> **How well should the system perform?**

Examples include:

- Scalability
- Availability
- Reliability
- Latency
- Durability
- Security
- Consistency
- Cost

Unlike functional requirements, these qualities often compete with one another.

Example:

Higher availability may require additional infrastructure, increasing cost.

---

# Functional vs. Non-Functional

| Functional | Non-Functional |
|-------------|----------------|
| Upload photo | Upload completes in under 2 seconds |
| Search users | Search responds within 100 ms |
| Send message | 99.99% availability |
| Store files | Data remains durable across failures |
| View feed | Feed loads quickly under heavy traffic |

A useful rule:

> Functional requirements define **what** to build.

> Non-functional requirements define **how well** it should work.

---

# Example Interview

**Interviewer**

> Design Instagram.

**Candidate**

> Before discussing the architecture, I'd like to clarify the requirements.
>
> - Are we designing photo sharing only?
> - Should users have private accounts?
> - Do we support Stories?
> - Are notifications required?
> - Approximately how many daily active users should I assume?

This immediately demonstrates a structured approach and helps avoid incorrect assumptions.

---

# Requirements vs. Clarifying Questions

One of the most common mistakes in system design interviews is confusing requirements with questions you need to ask the interviewer.

Requirements describe what the system should do or how well it should perform.

Clarifying questions reduce uncertainty before you begin designing.

| Example | Requirement | Clarifying Question |
|---------|-------------|---------------------|
| Upload files | ✅ | |
| Share folders | ✅ | |
| High availability | ✅ | |
| Maximum file size? | | ✅ |
| Should deleted files be recoverable? | | ✅ |
| Do users collaborate in real time? | | ✅ |
| Is there a free tier? | | ✅ |

> **Rule of thumb:** If the interviewer hasn't specified it and the answer could change your architecture, ask before making assumptions.

---

## 💡 The Golden Rule

```text
Never assume.

Clarify first.

Estimate second.

Design third.
```

---

## Common Non-Functional Requirements

### Scalability

**Question**

Can the system continue performing as traffic and user growth increase?

**Example**

An application should continue handling requests even after its user base grows from thousands to millions.

---

### Availability

**Question**

Can users access the system when failures occur?

**Example**

A shopping website should continue serving customers even if one application server crashes.

---

### Reliability

**Question**

Does the system behave correctly and consistently over time?

**Example**

Every successfully placed order should be processed exactly once.

---

### Latency

**Question**

How quickly does the system respond?

**Example**

Users expect search results to appear within a few hundred milliseconds.

---

### Durability

**Question**

Will data survive crashes and failures?

**Example**

Uploaded files should not be lost if a storage server fails.

---

### Security

**Question**

Can users trust the system to protect their data?

**Example**

Personal documents should only be accessible by authorized users.

---

### Consistency

**Question**

Do all users observe the correct version of the data?

**Example**

A bank account balance should remain consistent across all services after a transfer.

---

# Requirements Create Trade-offs

Every important non-functional requirement has a cost.

Understanding these trade-offs is one of the strongest indicators of good system design.

```text
High Availability
        │
        ▼
Replication
        │
        ▼
Higher Infrastructure Cost

----------------------------

Low Latency
        │
        ▼
Caching
        │
        ▼
Cache Invalidation Complexity

----------------------------

High Durability
        │
        ▼
Multiple Copies
        │
        ▼
Higher Storage Cost
```

Good architects don't just identify trade-offs—they explain why those trade-offs are worthwhile.

---

# Prioritizing Requirements

Not every requirement has equal importance.

Different products prioritize different qualities.

### Banking System

Priority:

- Consistency ⭐⭐⭐⭐⭐
- Security ⭐⭐⭐⭐⭐
- Availability ⭐⭐⭐⭐☆

Latency is important—but not at the expense of incorrect account balances.

---

### Social Media Feed

Priority:

- Availability ⭐⭐⭐⭐⭐
- Scalability ⭐⭐⭐⭐⭐
- Low Latency ⭐⭐⭐⭐⭐

A post appearing a few seconds later is usually acceptable.

---

### Messaging App

Priority:

- Reliability ⭐⭐⭐⭐⭐
- Low Latency ⭐⭐⭐⭐⭐
- Availability ⭐⭐⭐⭐☆

The architecture changes depending on which qualities matter most.

---

## ⭐ Interview Insight

Interviewers rarely expect you to list every possible requirement.

Instead, they want to see whether you can identify the **important** ones.

Ask yourself:

> Which requirements will have the greatest influence on my architecture?

Those are the ones worth discussing.

---

## ✅ Interview Checklist

Before drawing your first box, ask yourself:

- □ Do I understand the product?
- □ Do I know the core features?
- □ Have I identified the important quality attributes?
- □ Have I asked clarifying questions?
- □ Which requirements will most influence my design?

---

## ⚠️ Interview Traps

❌ Starting with technology choices.

❌ Listing every possible requirement.

❌ Ignoring non-functional requirements.

❌ Assuming every system values consistency over availability (or vice versa).

❌ Forgetting to prioritize requirements.

---

## ☕ Backend Java Lens

When designing Spring Boot applications, requirements influence both architecture and implementation.

| Requirement | Possible Design Choice |
|-------------|------------------------|
| High Availability | Multiple application instances behind a load balancer |
| Low Latency | Redis caching |
| Reliability | Retry policies and circuit breakers |
| Security | Spring Security with OAuth2/JWT |
| Scalability | Stateless services with horizontal scaling |

Frameworks don't determine the architecture.

Requirements do.

---

## 📝 Whiteboard Sketch

```text
                  Requirements

          ┌──────────┴──────────┐
          ▼                     ▼

     Functional         Non-Functional

          \               /

           \             /

            ▼           ▼

      Architecture Decisions
```

---
## 🔜 What's Next?

Now that you've identified the system requirements, the next step is understanding the expected scale.

Questions like these come next:

- Is the workload read-heavy or write-heavy?
- How much data will the system store?
- How much traffic should it handle?
- Are users distributed globally?

These aren't requirements—they're characteristics of the workload that help determine the appropriate architecture.

The next playbook introduces capacity estimation and shows how experienced engineers use these observations to guide design decisions.

---
## 🎯 30-Second Recap

- ✔ Functional requirements describe **what** the system does.
- ✔ Non-functional requirements describe **how well** it does it.
- ✔ Requirements should drive architecture.
- ✔ Clarify before making assumptions.
- ✔ Prioritize requirements before choosing technologies.
- ✔ Different systems prioritize different qualities.

---

## 💬 Practice Exercise

Your interviewer says:

> Design an online food delivery platform.

Without drawing any architecture:

1. List **6 functional requirements**.
2. List **6 non-functional requirements**.
3. Choose the **top 3** non-functional requirements.
4. Explain why those three are the most important.

---

## 💡 Key Takeaway

> **Architecture is the consequence of requirements. If you don't understand the requirements, every technology choice is just a guess.**
