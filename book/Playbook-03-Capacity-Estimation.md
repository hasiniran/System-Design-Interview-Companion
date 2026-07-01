# Playbook 03 – Capacity Estimation

> Capacity estimation isn't about getting exact numbers.
>
> It's about estimating the **order of magnitude** so you can choose the right architecture.

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 12–15 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Interview Expectation** | Capacity Estimation |
| **Difficulty** | Intermediate |
| **Prerequisites** | Playbook 02 – Functional vs. Non-Functional Requirements |

---

## 📌 What Problem Does This Solve?

A common interview mistake is jumping directly into databases, caches, and load balancers.

Strong candidates first estimate the expected scale of the system.

Capacity estimation helps answer:

- How much traffic?
- How much storage?
- How much bandwidth?
- What kind of workload?
- How large is the system?

Those estimates drive architecture.

---

# 🧠 Mental Model

```
Understand Product
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

Never estimate before understanding the product and its workload.

---

# Product Characteristics

Before calculating anything, understand the workload.

| Characteristic | Why It Matters |
|----------------|----------------|
| Read-heavy vs Write-heavy | Cache strategy |
| Large vs Small Objects | Storage choice |
| Global vs Regional | CDN / Multi-region |
| Real-time vs Async | Communication model |
| Predictable vs Bursty | Autoscaling |

Before estimating, ask yourself:

- How many users?
- How frequently do they use the feature?
- How large is the average object?
- How long is data retained?
- Is the workload read-heavy or write-heavy?

> 💡 **Think Like an Architect**
>
> Every characteristic should point toward an architectural decision.
>
> - Read-heavy → Cache
> - Large objects → Object Storage
> - Global users → CDN
> - High write throughput → Queue

---

# Capacity Estimation Toolkit

| Estimate | Formula | Why It Matters |
|-----------|---------|----------------|
| Average QPS | Requests/day ÷ 86,400 | Baseline server sizing |
| Peak QPS | 3–5 × Average QPS* | Autoscaling & load balancing |
| Storage | Users × Objects × Size | Storage architecture |
| Bandwidth | Peak QPS × Response Size | Network & CDN |
| Cache Size | Cached Objects × Size | Redis sizing |

> *Assume **3–5×** unless the interviewer specifies traffic patterns. Explain your assumption.

📌 Need a quick refresher on binary units, powers of two, and common estimation formulas? See the Capacity Estimation Cheat Sheet.
---

# Worked Example – Dropbox

**Interviewer**

> Design Dropbox.

**Candidate**

I'll make a few assumptions before estimating capacity.

- 5 million daily active users
- 1 upload per user per day
- Average file size = 5 MB
- Read-heavy workload

### Storage

```
5M × 1 × 5 MB

≈ 25 TB/day

≈ 9 PB/year
```

### Average QPS

Assume 20 million requests/day.

```
20,000,000 ÷ 86,400

≈ 231 QPS
```

### Peak QPS

```
231 × 5

≈ 1,155 QPS
```

### Architectural Conclusions

- Store files in Object Storage.
- Store metadata separately in a relational database.
- Introduce caching because reads dominate.
- Design for high durability.

Notice how every estimate influenced a design decision.

---

## ⭐ Interview Insight

Interviewers care more about your reasoning than your arithmetic.

A strong answer follows this pattern:

1. State assumptions.
2. Perform quick estimates.
3. Explain what the numbers tell you.
4. Connect every estimate to the architecture.

---

## ✅ Interview Checklist

- □ Did I understand the workload?
- □ Did I state assumptions?
- □ Did I estimate storage?
- □ Did I estimate traffic?
- □ Did I estimate Peak QPS?
- □ Did every estimate influence my architecture?

---

## ⚠️ Interview Traps

- Starting calculations before clarifying assumptions.
- Treating estimates as exact values.
- Spending too much time on arithmetic.
- Calculating numbers that never influence the design.

---

## ☕ Backend Java Lens

| Observation | Possible Design Choice |
|-------------|------------------------|
| Read-heavy | Redis |
| Large files | Object Storage |
| High writes | Kafka |
| Massive traffic | Stateless Spring Boot + Load Balancer |
| Global users | CDN |

---

## 📝 Whiteboard Sketch

```
Product
   │
   ▼
Workload
   │
   ▼
Capacity
   │
   ▼
Architecture
```

---

## 🎯 30-Second Recap

- Capacity estimation determines scale.
- Estimate only what influences the architecture.
- State assumptions before calculating.
- Use quick, rounded numbers.
- Every estimate should justify a design decision.

---

## 💬 Practice Exercise

Design Google Drive.

1. Identify the product characteristics.
2. State your assumptions.
3. Estimate:
   - Storage
   - Average QPS
   - Peak QPS
4. Explain how those estimates influence your architecture.

---

## 💡 Key Takeaway

> Capacity estimation is not a math exercise. It is the bridge between understanding the problem and choosing the right architecture.
