# Workbook 07 – Caching

> Practice caching decisions without repeating the full system-design workflow.

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 20–25 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Focus** | What to Cache • Cache Patterns • TTL • Invalidation • Failure Handling |
| **Prerequisites** | Playbook 07 – Caching |
| **Recommended Format** | Short written answers + brief verbal explanation |

---

## How to Use This Workbook

Do **not** redesign the entire system.

Focus only on the caching decisions:

```text
Repeated / Expensive Read
        ↓
Should It Be Cached?
        ↓
Cache Key
        ↓
Cache Pattern
        ↓
TTL / Invalidation
        ↓
Failure Mode
        ↓
Trade-off
```

Keep most answers to one or two sentences.

---

# Exercise 1 – Should This Be Cached?

For each scenario:

1. Decide whether caching is appropriate.
2. Explain why.
3. State whether stale data is acceptable.

## Scenario 1 – Product Description

Product descriptions are read millions of times per day and change only a few times per week.

```text
Should cache:

Why:

Staleness tolerance:
```

## Scenario 2 – Bank Balance

A user's current account balance is displayed before a money transfer.

```text
Should cache:

Why:

Staleness tolerance:
```

## Scenario 3 – Search Suggestions

The same popular search prefixes are requested repeatedly.

```text
Should cache:

Why:

Staleness tolerance:
```

## Scenario 4 – Seat Availability

Users browse seat availability before booking.

The final reservation operation must prevent double booking.

```text
Should cache:

Why:

What must still use the source of truth:
```

## Scenario 5 – Authorization Permissions

Permissions change rarely, but stale permissions could allow unauthorized access.

```text
Should cache:

Why:

Staleness tolerance:

Additional safeguard:
```

---

# Exercise 2 – Choose the Cache Pattern

Choose the best fit:

- Cache-aside
- Read-through
- Write-through
- Write-behind

Explain briefly.

## Scenario 1 – Product Catalog

Product details are read frequently and updated occasionally.

The database should remain authoritative.

```text
Pattern:

Why:

Write behavior:
```

## Scenario 2 – Configuration Store

Applications always read configuration through a shared cache layer.

The cache infrastructure can automatically fetch missing values.

```text
Pattern:

Why:
```

## Scenario 3 – Frequently Updated Reference Data

The cache should be immediately updated whenever the backing store is updated.

Higher write latency is acceptable.

```text
Pattern:

Why:

Trade-off:
```

## Scenario 4 – Analytics Counters

Very high write throughput is required.

A short delay before persistence is acceptable.

```text
Pattern:

Why:

Main risk:
```

---

# Exercise 3 – TTL and Invalidation

For each scenario, choose:

1. Short / medium / long TTL
2. Whether explicit invalidation is needed
3. Why

## Scenario 1 – Country List

Country data almost never changes.

```text
TTL:

Invalidation:

Why:
```

## Scenario 2 – Product Description

Changes a few times per week.

```text
TTL:

Invalidation:

Why:
```

## Scenario 3 – Product Price

Changes occasionally.

Users may tolerate a slightly stale browse page, but checkout must use the latest price.

```text
TTL:

Invalidation:

Checkout behavior:
```

## Scenario 4 – Inventory Preview

Inventory changes frequently during a flash sale.

```text
TTL:

Invalidation:

Browse behavior:

Checkout behavior:
```

## Scenario 5 – Missing User Record

Requests repeatedly ask for a user ID that does not exist.

```text
Caching strategy:

TTL:

Why:
```

---

# Exercise 4 – Diagnose the Cache Problem

Identify the issue and propose one mitigation.

## Scenario 1 – Cache Stampede

A popular product key expires.

Thousands of requests immediately miss and query the database.

```text
Problem:

Mitigation:

Trade-off:
```

## Scenario 2 – Hot Key

A celebrity post receives millions of requests.

All requests hit the same cache key and therefore the same cache node.

```text
Problem:

Mitigation:

Trade-off:
```

## Scenario 3 – Cache Outage

The distributed cache becomes unavailable.

All application instances immediately query the primary database.

```text
Problem:

Mitigation:

Why:
```

## Scenario 4 – Stale Price

The database contains:

```text
price = $80
```

The cache contains:

```text
price = $100
```

```text
Problem:

Likely cause:

Mitigation:

Critical operation that must use authoritative data:
```

## Scenario 5 – Empty Cache After Restart

The cache cluster restarts and comes back empty.

Traffic immediately begins falling through to the database.

```text
Problem:

Mitigation:
```

---

# Exercise 5 – Cache Miss vs Stale Cache

Complete the table.

| Situation | Cache Miss or Stale Cache? | User Impact | Source DB Impact |
|---|---|---|---|
| Key does not exist in cache | | | |
| Cache contains old product price | | | |
| Cache entry expired | | | |
| Cache contains old permissions | | | |
| Negative-cache entry still says NOT_FOUND after object creation | | | |

---

# Exercise 6 – Design a Cache Key

## 6.1 Product Details

```text
Query:
Get product by product_id

Cache key:
```

## 6.2 User Profile

```text
Query:
Get profile by user_id

Cache key:
```

## 6.3 Search Suggestions

```text
Query:
Get suggestions for prefix = "iph"

Cache key:
```

## 6.4 Versioned Product Data

Product responses change when a new version is published.

```text
Cache key:
```

---

# Exercise 7 – Two-Minute Interview Response

## Scenario: Product Catalog

Requirements:

- Product pages receive heavy read traffic.
- Product descriptions change rarely.
- Prices change occasionally.
- Inventory changes frequently.
- Checkout must use correct price and inventory.
- Cache failure must not bring down the database.

Do not redesign the full system.

Cover only caching:

1. What would you cache?
2. What would you not trust from cache?
3. Cache key
4. Cache pattern
5. TTL
6. Invalidation
7. Failure handling
8. One important trade-off

```text
What to cache:

What remains authoritative:

Cache key:

Pattern:

TTL:

Invalidation:

Failure handling:

Trade-off:
```

---

# Rapid Recall – 5 Minutes

Answer each in one sentence.

## 1. What is the main purpose of caching?

```text
Answer:
```

## 2. What is the difference between a cache miss and stale cache?

```text
Answer:
```

## 3. Why is cache-aside a common default?

```text
Answer:
```

## 4. Why is deleting a cache entry often safer than updating both DB and cache?

```text
Answer:
```

## 5. What determines a good TTL?

```text
Answer:
```

## 6. What is a cache stampede?

```text
Answer:
```

## 7. What is a hot key?

```text
Answer:
```

## 8. Why can a cache outage overload the database?

```text
Answer:
```

## 9. What is negative caching?

```text
Answer:
```

## 10. Why should critical operations verify against the source of truth?

```text
Answer:
```

---

# Completion Check

You are ready to move to Playbook 08 when you can explain:

- What data benefits from caching
- What data should not be trusted blindly from cache
- Cache-aside vs read-through vs write-through vs write-behind
- How TTL affects freshness and hit rate
- How invalidation works
- Cache miss vs stale cache
- Cache stampede vs hot key
- What happens when the cache fails
- Why the source of truth remains authoritative

You do not need to memorize specific cache products.

The goal is to justify the caching strategy and its failure behavior.

---

## Final Reflection

Which caching decision still feels least automatic?

```text
Answer:
```

What is the most important rule you want to remember in an interview?

```text
Answer:
```
