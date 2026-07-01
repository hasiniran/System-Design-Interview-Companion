# Workbook 03 – Capacity Estimation

> This workbook is designed to simulate the first 10–15 minutes of a real system design interview.
>
> Focus on explaining your reasoning rather than performing perfect calculations.

---

## 🎯 Learning Objectives

After completing this workbook, you should be able to:

- Identify the information needed before estimating capacity.
- Make reasonable assumptions.
- Estimate storage and traffic.
- Connect every estimate to an architectural decision.
- Communicate your reasoning clearly.

---

# Exercise 1 – Google Drive

## 📝 Product Summary

Describe the product in one or two sentences.

What is the core purpose of the system?

```
____________________________________________________

____________________________________________________
```

---

## Functional Requirements

Group related functionality together.

### Content Management

```
____________________________________________________

____________________________________________________
```

### Collaboration

```
____________________________________________________

____________________________________________________
```

### User Management

```
____________________________________________________

____________________________________________________
```

---

## Non-Functional Requirements

List the qualities that will influence your architecture.

```
____________________________________________________

____________________________________________________

____________________________________________________
```

---

## Clarifying Questions

What would you ask before designing the system?

```
____________________________________________________

____________________________________________________

____________________________________________________
```

---

## Product Characteristics

Identify the characteristics that will influence your architecture.

| Characteristic | ✓ |
|---------------|---|
| Read-heavy | |
| Write-heavy | |
| Large Objects | |
| Small Objects | |
| Global Users | |
| Regional Users | |
| Real-time | |
| Asynchronous | |
| Bursty Traffic | |
| Predictable Traffic | |

Other observations:

```
____________________________________________________

____________________________________________________
```

---

## Capacity Assumptions

| Assumption | Value |
|------------|-------|
| Registered Users | |
| Daily Active Users | |
| Requests per User per Day | |
| Writes per User per Day | |
| Average Object Size | |
| Data Retention | |

---

# Capacity Estimation

## Storage

Formula

```
Users × Objects × Size
```

Calculation

```
____________________________________________________

____________________________________________________
```

Architecture Decision

```
____________________________________________________

____________________________________________________
```

---

## Average QPS

Formula

```
Requests per Day ÷ 86,400
```

Calculation

```
____________________________________________________
```

Architecture Decision

```
____________________________________________________
```

---

## Peak QPS

Formula

```
Average QPS × (3–5)
```

Calculation

```
____________________________________________________
```

Architecture Decision

```
____________________________________________________
```

---

## Bandwidth (Optional)

Estimate only if it influences your design.

Calculation

```
____________________________________________________
```

Architecture Decision

```
____________________________________________________
```

---

# 🧠 Architecture Decision Log

| Observation | Architecture Decision |
|-------------|-----------------------|
| | |
| | |
| | |
| | |
| | |

---

# 🎯 Biggest Constraint

Which estimate had the biggest impact on your design?

☐ Storage

☐ Traffic

☐ Latency

☐ Reliability

☐ Availability

☐ Other

Why?

```
____________________________________________________

____________________________________________________
```

---

# 🎤 Final Interview Answer

Imagine you're speaking to the interviewer.

Summarize your design in **5–8 sentences**.

Focus on:

- assumptions
- estimates
- architectural decisions
- trade-offs

```
____________________________________________________

____________________________________________________

____________________________________________________

____________________________________________________

____________________________________________________
```

---

# 💭 Reflection

What assumption had the biggest impact?

```
____________________________________________________
```

What trade-off did you make?

```
____________________________________________________
```

What would you ask the interviewer next?

```
____________________________________________________
```

---

## ✅ Self Review

Before moving on, check the following:

- ☐ I stated assumptions before calculating.
- ☐ I estimated only what influenced the architecture.
- ☐ Every estimate led to an architectural decision.
- ☐ I explained *why* I chose each component.
- ☐ I identified the biggest system constraint.
- ☐ My final answer focused on reasoning instead of technology names.

---

## ⭐ Golden Rule

> **Capacity estimation is valuable only if it changes your architecture.**

If a calculation doesn't influence a design decision, you probably didn't need to calculate it.
