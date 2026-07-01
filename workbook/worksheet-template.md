# System Design Interview Worksheet

> Use this worksheet to structure your thoughts during a system design interview.

---

# 📝 Product Summary

Describe the system in one or two sentences.

```
____________________________________________________

____________________________________________________
```

---

# Functional Requirements

Group related functionality.

### __________________________

```
____________________________________________________

____________________________________________________
```

### __________________________

```
____________________________________________________

____________________________________________________
```

### __________________________

```
____________________________________________________

____________________________________________________
```

---

# Non-Functional Requirements

List the qualities that will influence the architecture.

```
____________________________________________________

____________________________________________________

____________________________________________________
```

---

# Clarifying Questions

What should you ask before designing the system?

```
____________________________________________________

____________________________________________________

____________________________________________________
```

---

# Product Characteristics

Check the characteristics that apply.

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

# Capacity Assumptions

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
```

Architecture Decision

```
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

# 🏗 High-Level Architecture

Sketch the major components.

```text
                _______________________

                        Users
                          │
                          ▼

___________________________________________________________

___________________________________________________________

___________________________________________________________

___________________________________________________________
```

---

# 🎯 Biggest Constraint

Which estimate most influenced your design?

☐ Storage

☐ Traffic

☐ Latency

☐ Reliability

☐ Availability

☐ Consistency

☐ Cost

☐ Other

Why?

```
____________________________________________________

____________________________________________________
```

---

# ⚖ Trade-offs

What trade-offs did you make?

```
____________________________________________________

____________________________________________________
```

---

# 🎤 Final Interview Summary

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
