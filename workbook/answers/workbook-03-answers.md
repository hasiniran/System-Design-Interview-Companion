# Workbook 03 – Sample Answers

> These are **sample answers**, not the only correct answers.
>
> In system design interviews, assumptions and architectural decisions may vary.
> The important part is that your assumptions are reasonable and every estimate leads to a justified architectural decision.

---

# Exercise 1 – Workload Characteristics

| Product | Read Heavy | Write Heavy | Large Objects | Global | Real-time | Storage Intensive |
|----------|------------|-------------|---------------|----------|-----------|-------------------|
| Google Drive | ✅ | ✅ | ✅ | ✅ | ❌* | ✅ |
| Twitter | ✅ | ✅ | ❌ | ✅ | ❌ | ❌ |
| YouTube | ✅ | ✅ | ✅ | ✅ | ✅** | ✅ |
| WhatsApp | ✅ | ✅ | ❌ | ✅ | ✅ | ✅ |
| Uber | ✅ | ✅ | ❌ | ✅ | ✅ | ❌ |

\* Unless collaborative editing is in scope.

\** Live streaming introduces real-time requirements.

### Sample Reflection

**WhatsApp**

- Messages are small, so it is **not a large-object system**.
- Every sent message generates writes, while recipients and synchronized devices generate reads.
- Users expect low latency, making it a near real-time system.
- Message history makes the system storage intensive.

---

# Exercise 2 – Making Assumptions

## Sample Assumptions

| Assumption | Value |
|------------|-------|
| Registered Users | 100M |
| Daily Active Users | 20M |
| Requests / User / Day | 50 |
| Posts / User / Day | 1 |
| Average Photo Size | 500 KB |
| Retention Period | Indefinite |

### Why These Are Reasonable

- Instagram is a mature global platform with a large user base.
- A significant percentage of users access the platform daily.
- Viewing feeds, profiles, comments, and notifications generates many API requests.
- Most users consume more content than they create, so posting frequency is relatively low.
- Users expect uploaded content to remain available until deleted.

---

# Exercise 3 – Capacity Estimation

## Product Summary

Secure, durable cloud storage that allows users to upload, synchronize, share, and collaborate on files across multiple devices.

---

## Workload Characteristics

- Large binary objects
- Long-term retention
- Global users
- Read-heavy with significant writes
- Bursty upload traffic

---

## Sample Assumptions

| Assumption | Value |
|------------|-------|
| Registered Users | 100M |
| Daily Active Users | 10M |
| Requests / User / Day | 20 |
| Uploads / User / Day | 1 |
| Average File Size | 200 MB |

---

## Storage

```
10M × 1 × 200 MB

≈ 2 PB/day

≈ 730 PB/year
```

### Architecture Decision

- Store binary file contents in **object storage**.
- Store metadata in a **relational database** for efficient querying, indexing, and permissions.

---

## Average QPS

```
10M × 20 ÷ 86,400

≈ 2,315 QPS
```

### Architecture Decision

Deploy multiple stateless application instances behind a load balancer.

---

## Peak QPS

```
≈ 3 × 2,315

≈ 7,000 QPS
```

### Architecture Decision

Use horizontal scaling with autoscaling to absorb traffic spikes while controlling costs.

---

## Biggest Constraint

**Storage**

The system grows by hundreds of petabytes per year.

This makes scalable object storage the primary architectural decision.

---

## Architecture Summary

Because the system has:

- Large binary objects
- Massive storage growth
- Global users
- Read-heavy traffic

I would use:

- Object storage
- Relational database for metadata
- Stateless application servers
- Load balancer
- CDN
- Caching

---

# Exercise 4 – Architecture Decision Drills

| Observation | Sample Architecture | Why? | Trade-off |
|-------------|--------------------|------|-----------|
| 120 PB/year | Object Storage + Relational DB | Object storage scales efficiently for large binary files while the database stores metadata. | Two storage systems must be maintained. |
| 45K Peak QPS | Load Balancer + Stateless Services + Autoscaling | Horizontal scaling handles traffic spikes. | Increased infrastructure complexity and cost. |
| 95% Reads | Cache | Reduces latency and database load. | Cache invalidation and consistency become more complex. |
| Global Users | Multi-region Deployment + CDN | Reduces latency and improves availability. | Higher operational cost and replication complexity. |
| Large uploads should not block users | Asynchronous processing with message queues and background workers (multipart uploads for large files) | Users receive an immediate response while processing continues in the background. | More components and eventual consistency for background tasks. |

---

# Quick Fire

| Observation | Sample Architecture |
|-------------|---------------------|
| 500 GB total storage | Relational Database |
| 150 PB storage | Object Storage |
| 25 QPS | Single Server |
| 20K Peak QPS | Load Balancer + Multiple Stateless Services |
| Global users | CDN |
| Read-heavy workload | Cache |
| Write-heavy workload | Message Queue |
| Large binary objects | Object Storage + Relational Database (Metadata) |
| Frequently accessed data | Cache |
| Background processing | Message Queue + Background Workers |
| High durability | Replication |
| Long-term storage | Object Storage + Lifecycle Policies |
| Low latency | Cache *(or CDN depending on the bottleneck)* |
| Real-time communication | WebSocket |
| Metadata lookups | Relational Database |
| High write throughput | Message Queue + Database Partitioning |
| Multi-region availability | Multi-region Deployment + CDN |
| Frequently changing data | Cache (e.g., Redis) |

---

# Key Lessons

- Understand the product before estimating capacity.
- Identify the workload characteristics.
- Make reasonable assumptions and state them explicitly.
- Estimate only the numbers that influence your architecture.
- Every estimate should lead to an architectural decision.
- Explain *why* you chose a technology, not just *what* you chose.
- Be prepared to discuss the trade-offs introduced by your decisions.
