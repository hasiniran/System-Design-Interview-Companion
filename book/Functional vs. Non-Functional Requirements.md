# Functional vs. Non-Functional Requirements

> The first few minutes of a system design interview determine the direction of the entire discussion.

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 8 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Interview Expectation** | Design |
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

# 🏗️ Requirements Drive Architecture

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

They answer:

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

A good functional requirement is observable.

You can usually answer it with:

> **"Can the user do this?"**

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

Unlike functional requirements, these often compete with one another.

Example:

Higher availability may require additional infrastructure, increasing cost.

---

# Functional vs. Non-Functional

| Functional | Non-Functional |
|-------------|----------------|
| Upload photo | Upload completes in under 2 seconds |
| Search users | Search responds within 100 ms |
| Send message | 99.99% availability |
| Store files | Data is durable across failures |
| View feed | Feed loads quickly under heavy traffic |

A useful rule:

> Functional requirements define **what** to build.

> Non-functional requirements define **how well** it should work.

---

# Example Interview

**Interviewer**

> Design Instagram.

Weak response:

> "I'd use PostgreSQL, Redis, Kafka..."

Strong response:

> "Before discussing architecture, I'd like to clarify the functional and non-functional requirements."

This immediately demonstrates a structured approach.

---

# Common Non-Functional Requirements

## Scalability

Can the system continue performing as users and traffic increase?

---

## Availability

Can users access the system even if some components fail?

---

## Reliability

Does the system behave correctly and consistently?

---

## Latency

How quickly does the system respond?

---

## Durability

Will data survive crashes and failures?

---

## Security

Can users trust the system to protect their data?

---

## Consistency

Do all users see the same data at the same time?

---

# Prioritizing Requirements

Not every requirement has equal importance.

For example:

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

---

The architecture changes depending on which qualities matter most.

---

## ⭐ Interview Insight

Interviewers rarely expect you to list every possible requirement.

Instead, they want to see whether you can identify the **important** ones.

Ask yourself:

> Which requirements will influence my design?

Those are the ones worth discussing.

---

## ⚠️ Interview Traps

❌ Starting with technology choices.

❌ Listing every possible requirement.

❌ Ignoring non-functional requirements.

❌ Assuming every system values consistency over availability (or vice versa).

❌ Forgetting to prioritize.

---

## ☕ Backend Java Lens

When designing a Spring Boot service, requirements influence implementation.

Examples:

| Requirement | Possible Design Choice |
|-------------|------------------------|
| High Availability | Multiple application instances behind a load balancer |
| Low Latency | Introduce Redis caching |
| Reliability | Retry policies and circuit breakers |
| Security | Spring Security with OAuth2/JWT |
| Scalability | Stateless services with horizontal scaling |

Notice that frameworks don't determine the architecture.

Requirements do.

---

## 📝 Whiteboard Sketch

```text
            Requirements

        Functional
             │
             ▼

     System Features

             │

             ▼

 Architecture Decisions

             ▲

             │

 Non-Functional
```

---

## 🎯 30-Second Recap

- ✔ Functional requirements describe **what** the system does.
- ✔ Non-functional requirements describe **how well** it does it.
- ✔ Requirements should drive architecture.
- ✔ Prioritize requirements before choosing technologies.
- ✔ Different systems prioritize different qualities.
- ✔ Clarifying requirements is one of the strongest signals in a system design interview.

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
