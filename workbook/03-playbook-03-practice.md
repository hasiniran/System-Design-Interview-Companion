# Workbook 03 – Capacity Estimation

> Practice translating product characteristics into realistic capacity estimates and architectural decisions.

---

## 📖 Study Card

| | |
|---|---|
| **Estimated Time** | 35–45 minutes |
| **Companion Playbook** | Playbook 03 – Capacity Estimation |
| **Difficulty** | Intermediate |
| **Exercises** | 5 |

---

# 🎯 Learning Objectives

After completing this workbook, you should be able to:

- Identify product characteristics that influence scale.
- Make reasonable assumptions when information is missing.
- Estimate storage and traffic.
- Explain architectural decisions using your estimates.
- Communicate your reasoning like an interview candidate.

---

# Before You Begin

Review:

- Playbook 03
- Capacity Estimation Cheat Sheet
- System Design Numbers Cheat Sheet

Recommended timing:

| Exercise | Time |
|----------|------|
| 1 | 5 min |
| 2 | 5 min |
| 3 | 10 min |
| 4 | 10 min |
| 5 | 15 min |

Don't aim for perfect math.

Aim for **reasonable assumptions** and **good engineering decisions**.

---

# Exercise 1 – Product Characteristics

For each product, identify the characteristics that influence your architecture.

| Product | Read Heavy | Write Heavy | Large Objects | Global | Real-time | Storage Intensive |
|----------|------------|-------------|---------------|----------|-----------|-------------------|
| Google Drive | | | | | | |
| Twitter | | | | | | |
| YouTube | | | | | | |
| WhatsApp | | | | | | |
| Uber | | | | | | |

---

### Reflection

For one product above, explain why you chose those characteristics.

```
____________________________________________________

____________________________________________________

____________________________________________________
```

---

# Exercise 2 – Making Assumptions

The interviewer asks:

> Design Instagram.

State reasonable assumptions.

| Assumption | Value |
|------------|-------|
| Registered Users | |
| Daily Active Users | |
| Requests / User / Day | |
| Posts / User / Day | |
| Average Photo Size | |
| Retention Period | |

---

Now answer:

Why are these assumptions reasonable?

```
____________________________________________________

____________________________________________________

____________________________________________________
```

---

# Exercise 3 – Capacity Estimation

## Scenario

Design Google Drive.

---

### Product Summary

Describe the system.

```
____________________________________________________

____________________________________________________
```

---

### Product Characteristics

```
____________________________________________________

____________________________________________________
```

---

### Assumptions

| Assumption | Value |
|------------|-------|
| Registered Users | |
| Daily Active Users | |
| Requests / User / Day | |
| Uploads / User / Day | |
| Average File Size | |

---

## Estimate Storage

```
____________________________________________________

____________________________________________________
```

Architecture Decision

```
____________________________________________________
```

---

## Estimate Average QPS

```
____________________________________________________
```

Architecture Decision

```
____________________________________________________
```

---

## Estimate Peak QPS

```
____________________________________________________
```

Architecture Decision

```
____________________________________________________
```

---

## Estimate Bandwidth (Optional)

```
____________________________________________________
```

Architecture Decision

```
____________________________________________________
```

---
# Exercise 4 – Architecture Decision Drills

For each observation:

1. Choose the most appropriate architectural component.
2. Explain **why** it solves the problem.
3. Identify **one trade-off** introduced by your decision.

---

### Scenario 1

Storage grows by **120 PB per year**.

**Architecture**

```
____________________________________________________
```

**Why?**

```
____________________________________________________

____________________________________________________
```

**Trade-off**

```
____________________________________________________
```

---

### Scenario 2

Peak traffic reaches **45,000 QPS**.

**Architecture**

```
____________________________________________________
```

**Why?**

```
____________________________________________________

____________________________________________________
```

**Trade-off**

```
____________________________________________________
```

---

### Scenario 3

95% of requests are reads.

**Architecture**

```
____________________________________________________
```

**Why?**

```
____________________________________________________

____________________________________________________
```

**Trade-off**

```
____________________________________________________
```

---

### Scenario 4

Users are distributed across North America, Europe, and Asia.

**Architecture**

```
____________________________________________________
```

**Why?**

```
____________________________________________________

____________________________________________________
```

**Trade-off**

```
____________________________________________________
```

---

### Scenario 5

Users upload large files, but uploads should never block the user interface.

**Architecture**

```
____________________________________________________
```

**Why?**

```
____________________________________________________

____________________________________________________
```

**Trade-off**

```
____________________________________________________
```
---
# ⚡ Quick Fire

For each observation, write the **first architectural component** that comes to mind.

| Observation | Architecture |
|-------------|--------------|
| 500 GB total storage | |
| 150 PB storage | |
| 25 QPS | |
| 20K Peak QPS | |
| Global users | |
| Read-heavy workload | |
| Write-heavy workload | |
| Large binary objects | |
| Frequently accessed data | |
| Background processing | |
| High durability | |
| Long-term storage | |
| Low latency | |
| Real-time communication | |
| Metadata lookups | |

---

### Reflection

Choose **one** answer above and explain **why** you selected it.

```
____________________________________________________

____________________________________________________
```

# 🧩 Observation → Decision

Complete the sentence.

---

Storage grows to **250 PB/year**.

> Therefore, I will ______________________________________ because ______________________________________.

---

Peak traffic reaches **80K QPS**.

> Therefore, I will ______________________________________ because ______________________________________.

---

95% of traffic consists of reads.

> Therefore, I will ______________________________________ because ______________________________________.

---

Users are distributed across five continents.

> Therefore, I will ______________________________________ because ______________________________________.

---

Files must survive data center failures.

> Therefore, I will ______________________________________ because ______________________________________.

---

# Exercise 5 – Interview Challenge

Choose **ONE** system.

- Google Photos
- OneDrive
- Dropbox

Using the Interview Worksheet, complete a full interview.

Remember to cover:

- Product summary
- Requirements
- Product characteristics
- Assumptions
- Capacity estimation
- Architectural decisions
- Biggest constraint
- Trade-offs

---

# 💭 Reflection

Which estimate influenced your design the most?

```
____________________________________________________
```

What assumption had the greatest impact?

```
____________________________________________________
```

Which calculation was the hardest?

```
____________________________________________________
```

If you repeated the interview tomorrow, what would you improve?

```
____________________________________________________

____________________________________________________
```

---

# Common Mistakes

- Starting calculations before understanding the product.
- Treating assumptions as facts.
- Performing calculations that never influence the architecture.
- Choosing technologies before estimating scale.
- Forgetting to explain *why* a technology was selected.

---

# ✅ Self Review

Before moving to the next playbook, confirm:

- ☐ I identified the product characteristics.
- ☐ I stated assumptions before calculating.
- ☐ Every estimate influenced an architectural decision.
- ☐ I explained my reasoning instead of only presenting numbers.
- ☐ I can estimate storage and traffic without a calculator.
- ☐ I can explain my design confidently in under five minutes.

---

# 🎯 Key Lessons

- Product characteristics determine what should be estimated.
- Capacity estimation is about order of magnitude, not precision.
- Every estimate should justify an architectural decision.
- Assumptions are expected—explain them clearly.
- Strong interview answers connect **observation → estimate → decision**.

---

## Next Steps

- Review your answers against the Playbook.
- Complete another system using the Interview Worksheet.
- Review the Capacity Estimation Cheat Sheet.
- Continue to **Playbook 04**.
