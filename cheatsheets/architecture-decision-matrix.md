# Architecture Decision Matrix

> A quick-reference guide for mapping product characteristics and system requirements to architectural decisions.

---

# 🧠 Core Principle

> **Architecture is a series of trade-offs.**

Every design decision should solve a specific problem.

Don't choose technologies because they're popular.

Choose them because they address a requirement or constraint.

---

# 🔍 Observation → Architecture

| If you discover... | Consider... | Because... |
|--------------------|-------------|------------|
| Millions of reads | Redis Cache | Reduce database load |
| Read-heavy workload | Cache + CDN | Most requests can bypass the database |
| Large binary files | Object Storage | Databases aren't optimized for blobs |
| Small structured data | Relational Database | Strong querying and relationships |
| Flexible schema | NoSQL Database | Easier schema evolution |
| High write throughput | Kafka / Message Queue | Smooth traffic spikes |
| Long-running tasks | Background Workers | Keep APIs responsive |
| Global users | CDN + Multi-region | Reduce latency |
| Rapid traffic growth | Horizontal Scaling | Scale application instances |
| Frequent database bottleneck | Read Replicas | Scale reads independently |
| Single database becoming too large | Sharding | Distribute data |
| Heavy analytics | Data Warehouse | Separate OLTP from analytics |
| Large file downloads | CDN | Reduce origin traffic |
| Expensive repeated computations | Cache | Avoid redundant work |
| Independent business domains | Microservices | Independent scaling and deployment |

---

# 📊 Requirement → Design Choice

| Requirement | Common Design Choice |
|-------------|----------------------|
| Low latency | Cache, CDN |
| High availability | Load Balancer + Multiple Instances |
| High durability | Replication + Backups |
| High scalability | Stateless Services |
| High throughput | Partitioning + Queues |
| Strong consistency | Relational Database |
| Eventual consistency | NoSQL + Async Replication |
| Fault tolerance | Redundancy + Retry Policies |
| Security | Authentication + Authorization + Encryption |

---

# 🧭 Decision Trees

Instead of memorizing technologies, learn the questions that lead you to them.

---

## 1️⃣ Choosing Storage

```text
Need persistent storage?
        │
        ▼
Large binary objects?
      │         │
     Yes       No
      │         │
Object      Structured data?
Storage         │
            Yes      No
             │        │
     Relational     NoSQL
      Database      Database
```

**Rule of Thumb**

- Large files (images, videos, documents) → Object Storage
- Structured relational data → SQL Database
- Flexible or rapidly evolving schema → NoSQL

---

## 2️⃣ Solving Performance Problems

```text
Performance issue?
        │
        ▼
Read bottleneck?
      │         │
     Yes       No
      │         │
 Cache     Write bottleneck?
                 │
             Yes     No
              │       │
        Queue /    Investigate
        Sharding   CPU/Network
```

**Rule of Thumb**

- Slow reads → Cache
- High write throughput → Queue or Partitioning
- Always identify the bottleneck before choosing a solution.

---

## 3️⃣ Scaling the System

```text
Traffic increasing?
        │
        ▼
Application?
      │        │
     Yes      No
      │        │
Horizontal   Database?
 Scaling         │
             Reads    Writes
               │         │
        Read Replica  Sharding
```

**Rule of Thumb**

- Scale stateless services horizontally.
- Scale reads with replicas.
- Scale writes with partitioning or sharding.

---

## 4️⃣ Communication Pattern

```text
Need communication?
        │
        ▼
Immediate response?
      │         │
     Yes       No
      │         │
 REST/gRPC   Can it be async?
                 │
             Yes      No
              │        │
     Queue / Event   Revisit
           Bus       Requirements
```

**Rule of Thumb**

- Immediate user response → Synchronous APIs
- Long-running or independent work → Asynchronous messaging

---

> 💡 **Interview Tip**
>
> Don't say:
>
> *"I'd use Redis."*
>
> Say:
>
> *"Since the system is read-heavy, introducing a cache like Redis would reduce database load."*
>
> Interviewers evaluate your reasoning more than your technology choices.
---

# 🌍 Workload Patterns

| Workload | Typical Architecture |
|----------|----------------------|
| Read-heavy | Cache + Read Replicas |
| Write-heavy | Queue + Partitioning |
| Large media | Object Storage + CDN |
| Chat / Messaging | WebSockets + Queue |
| Analytics | Stream Processing + Data Warehouse |
| Search | Search Engine (e.g., Elasticsearch/OpenSearch) |
| Notifications | Pub/Sub + Push Services |

---

# 🗄 Database Selection

| If you need... | Consider... |
|----------------|-------------|
| ACID transactions | Relational Database |
| Flexible schema | Document Database |
| Key lookups | Key-Value Store |
| Graph traversal | Graph Database |
| Full-text search | Search Engine |
| Time-series metrics | Time-Series Database |

---

# 🚀 Scaling Decisions

| Problem | Common Solution |
|---------|-----------------|
| CPU bottleneck | Horizontal scaling |
| Memory bottleneck | More cache |
| Database reads | Read replicas |
| Database writes | Sharding |
| Large static assets | CDN |
| Slow APIs | Async processing |
| Bursty traffic | Autoscaling |
| High network traffic | Compression + CDN |

---

# ⚠️ Common Anti-Patterns

| Smell | Better Approach |
|-------|-----------------|
| Store videos in MySQL | Object Storage |
| Cache everything | Cache only hot data |
| Use microservices immediately | Start with a modular monolith |
| Scale database vertically forever | Add replicas or shard |
| One giant service | Separate domains when justified |
| Synchronous communication everywhere | Introduce asynchronous messaging where appropriate |

---

# 🎯 Interview Workflow

```text
Requirements
      │
      ▼
Product Characteristics
      │
      ▼
Capacity Estimation
      │
      ▼
Identify Bottlenecks
      │
      ▼
Choose Architecture
      │
      ▼
Explain Trade-offs
```

---

# 💡 Golden Rules

✅ Requirements drive architecture.

✅ Every estimate should influence a design decision.

✅ Every technology should solve a problem.

✅ Explain *why* before *what*.

✅ Architecture is about trade-offs, not perfect solutions.

---

# 📝 30-Second Decision Checklist

□ What is the biggest bottleneck?

□ Is the workload read-heavy or write-heavy?

□ What component will fail first?

□ Can this be cached?

□ Should this be asynchronous?

□ Does this data need strong consistency?

□ Does this need horizontal scaling?

□ What trade-off am I making?

---

## ⭐ Remember

> **Interviewers are not evaluating whether you know Redis, Kafka, or Kubernetes.**

> **They are evaluating whether you know *when* and *why* to use them.**

That's what great system design is about.
