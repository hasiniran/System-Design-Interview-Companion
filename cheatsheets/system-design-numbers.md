# System Design Numbers Cheat Sheet

> A one-page reference for backend system design interviews.

---

# 🔢 Powers of Two

| Power | Exact Value | Approximation |
|-------:|------------:|---------------|
| 2¹⁰ | 1,024 | 1K |
| 2²⁰ | 1,048,576 | 1M |
| 2³⁰ | 1,073,741,824 | 1B |
| 2⁴⁰ | 1,099,511,627,776 | 1T |
| 2⁵⁰ | 1,125,899,906,842,624 | 1P |

> 💡 Interview Tip  
> Round aggressively. Order-of-magnitude estimates are expected.

---

# 💾 Data Size Reference

## Binary Units (Computers)

| Unit | Binary | Approximation |
|------|--------|---------------|
| 1 KB | 2¹⁰ B | ≈ 1,000 B |
| 1 MB | 2²⁰ B | ≈ 1,000 KB |
| 1 GB | 2³⁰ B | ≈ 1,000 MB |
| 1 TB | 2⁴⁰ B | ≈ 1,000 GB |
| 1 PB | 2⁵⁰ B | ≈ 1,000 TB |

---

# ⏱ Time Conversions

| Value | Seconds |
|-------|---------:|
| 1 minute | 60 |
| 1 hour | 3,600 |
| 1 day | 86,400 |
| 1 week | 604,800 |
| 1 month (~30 days) | 2.6 million |
| 1 year | 31.5 million |

---

# 📈 Capacity Estimation

| Estimate | Formula | Why? |
|-----------|---------|------|
| Average QPS | Requests/day ÷ 86,400 | Baseline traffic |
| Peak QPS | 3–5 × Average QPS* | Traffic spikes |
| Storage | Users × Objects × Size | Data growth |
| Bandwidth | Peak QPS × Response Size | Network sizing |
| Cache Size | Cached Objects × Size | Memory estimate |

> *Use **3–5×** unless the interviewer specifies traffic patterns.*

---

# 🌍 Scale Reference

| Users | Typical Product |
|-------:|-----------------|
| 10³ | Small application |
| 10⁵ | Startup |
| 10⁶ | Medium internet service |
| 10⁸ | Large internet platform |
| 10⁹ | Global platform |

---

# 🧠 Workload Patterns

| Observation | Typical Design Choice |
|-------------|-----------------------|
| Read-heavy | Redis / CDN |
| Write-heavy | Queue / Partitioning |
| Large files | Object Storage |
| Small objects | Database |
| Global users | CDN + Multi-region |
| Bursty traffic | Autoscaling |
| High durability | Replication + Backups |
| High consistency | Strong consistency protocols |

---

# 🏗 Observation → Architecture

| If you discover... | Consider... |
|--------------------|-------------|
| Millions of reads | Cache |
| Large binary files | Object Storage |
| Global traffic | CDN |
| High write throughput | Kafka / Queue |
| Growing database | Sharding |
| Slow repeated queries | Cache |
| Heavy analytics | Data Warehouse |
| Long-running work | Async Processing |

---

# ⚡ Common Latencies (Approximate)

| Operation | Latency |
|-----------|---------|
| CPU Cache | ~1 ns |
| RAM | ~100 ns |
| SSD | ~100 μs |
| Same Datacenter Network | ~0.5 ms |
| Cross-Region Network | 10–100 ms |
| Internet Request | 30–150 ms |

> You don't need to memorize these exactly—understanding the relative differences is what's important.

---

# 🎯 Common Interview Assumptions

Unless the interviewer specifies otherwise:

- State your assumptions explicitly.
- Round numbers.
- Reads often exceed writes for consumer applications.
- Peak traffic ≈ 3–5× average traffic.
- Design for future growth.
- Estimate only what influences the architecture.

---

# 📝 Interview Workflow

```text
Understand Product
        │
        ▼
Requirements
        │
        ▼
Product Characteristics
        │
        ▼
Assumptions
        │
        ▼
Capacity Estimation
        │
        ▼
High-Level Design
        │
        ▼
Deep Dive & Trade-offs
```

---

# ✅ 30-Second Interview Checklist

□ Clarify the requirements.

□ Identify product characteristics.

□ State assumptions.

□ Estimate storage.

□ Estimate Average QPS.

□ Estimate Peak QPS.

□ Connect estimates to architecture.

□ Explain trade-offs.

---

## 💡 Golden Rule

> **Every estimate should answer an architectural question.**
>
> If a calculation doesn't influence your design, you probably didn't need to calculate it.
