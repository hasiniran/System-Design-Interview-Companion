# Playbook 07 – Caching

> Use caching to reduce latency and database load—but only when you can explain what is cached, how it becomes stale, and how correctness is protected.

---

## 📖 Study Card

| | |
|---|---|
| **Study Time** | 25–30 minutes |
| **Priority** | ⭐⭐⭐⭐⭐ Essential |
| **Interview Expectation** | Read Scaling • Latency Reduction • Data Consistency • Failure Handling |
| **Difficulty** | Intermediate |
| **Prerequisites** | Playbook 05 – Relational Databases • Playbook 06 – NoSQL Database Families |

---

## 📌 What Problem Does Caching Solve?

Many systems repeatedly read the same data.

Without a cache:

```text
Client
  ↓
Application
  ↓
Database
```

Every request reaches the database.

With a cache:

```text
Client
  ↓
Application
  ↓
Cache
  │
  ├── Hit → return data
  │
  └── Miss → database → cache → return data
```

Caching can improve:

- Read latency
- Database load
- Throughput
- Resilience to traffic spikes

But caching introduces a new problem:

> The cache can become stale.

So the real interview question is not:

> "Should I add Redis?"

It is:

> "What should I cache, how will it be refreshed or invalidated, and what happens when the cached value is wrong or missing?"

---

## 🧠 Mental Model

```text
Identify Expensive / Repeated Reads
            │
            ▼
Choose What to Cache
            │
            ▼
Choose Cache Pattern
            │
            ▼
Define TTL / Invalidation
            │
            ▼
Handle Misses and Failures
            │
            ▼
Protect Against Staleness
```

A cache is normally derived data. The source of truth lives elsewhere.

---

# 1. When Caching Fits

Caching is valuable when:

- Reads dominate writes
- The same data is requested repeatedly
- Data changes less frequently than it is read
- Slight staleness is acceptable
- The underlying computation or query is expensive
- Low latency matters

Typical examples:

- Product details
- User profiles
- Configuration
- Search suggestions
- Popular posts
- Aggregated counters
- Session data
- API responses
- Computed recommendations

---

# 2. What Should Not Be Cached Blindly?

Avoid caching data without thinking through correctness.

Examples:

- Bank balance
- Inventory during checkout
- Final payment state
- Seat ownership during reservation
- Security-sensitive authorization decisions

These may still use caching in carefully controlled designs, but the authoritative operation should verify against the source of truth.

```text
Product page inventory
→ cached approximation may be acceptable

Checkout inventory validation
→ authoritative store
```

---

# 3. Cache Hit vs Cache Miss vs Stale Cache

## Cache Hit

```text
Request
  ↓
Cache contains key
  ↓
Return value
```

## Cache Miss

```text
Request
  ↓
Cache does not contain key
  ↓
Read source of truth
  ↓
Populate cache
  ↓
Return value
```

## Stale Cache

```text
Source of truth:
price = $50

Cache:
price = $45
```

Important distinction:

```text
Cache miss
→ no cached value
→ slower fallback read

Stale cache
→ old cached value
→ potentially incorrect result
```

---

# 4. Cache-Aside Pattern

Cache-aside is one of the most common interview patterns.

The application manages the cache directly.

## Read Flow

```text
Application
    │
    ├── Check Cache
    │      │
    │      ├── Hit → Return
    │      └── Miss
    │
    └── Read Database
           │
           └── Populate Cache
```

Pseudo-flow:

```text
value = cache.get(key)

if value exists:
    return value

value = database.get(key)
cache.set(key, value)

return value
```

### Advantages

- Simple
- Cache only stores requested data
- Database remains the source of truth
- Application controls cache behavior

### Trade-offs

- First read after expiration is slower
- Cache and database can become inconsistent
- Application must manage invalidation
- Cache misses can create traffic spikes

### Interview Language

> "I would use cache-aside because the database remains authoritative. On a miss, the application reads from the database and populates the cache."

---

# 5. Write Invalidation with Cache-Aside

Suppose product price changes.

A common flow:

```text
1. Update database
2. Invalidate cache key
```

```text
UPDATE product_price

↓

DELETE cache:product:123
```

The next read repopulates the cache.

## Why Delete Instead of Immediately Updating?

Updating both stores creates a dual-write problem:

```text
Database update succeeds
Cache update fails
```

Now the two copies disagree.

Deleting is often simpler:

```text
Database = source of truth
Cache = rebuildable
```

---

# 6. Read-Through Cache

With read-through caching, the application requests the value from the cache layer, and the cache itself loads missing values.

```text
Application
    ↓
Cache
    │
    ├── Hit → Return
    └── Miss → Database
                  ↓
               Cache
                  ↓
               Return
```

### Advantages

- Application code is simpler
- Loading logic is centralized

### Trade-offs

- Requires infrastructure supporting the pattern
- Application has less control over loading behavior

---

# 7. Write-Through Cache

Writes go through the cache layer and are synchronously written to the backing store.

```text
Application
    ↓
Cache
    ↓
Database
```

### Advantages

- Cache stays current
- Read-after-write behavior is easier

### Trade-offs

- Higher write latency
- Cache may contain data that is never read
- Cache becomes part of the write path

---

# 8. Write-Behind / Write-Back Cache

The application writes to the cache first, and persistence happens asynchronously.

```text
Application
    ↓
Cache
    ↓
Return Success
    ↓
Async Database Write
```

### Advantages

- Very low write latency
- Writes can be batched

### Trade-offs

- Risk of data loss if cache fails before persistence
- Eventual consistency
- More complex recovery
- Usually unsuitable for critical transactional data unless durability is carefully designed

---

# 9. Comparing Cache Patterns

| Pattern | Read Flow | Write Behavior | Main Strength | Main Trade-off |
|---|---|---|---|---|
| Cache-aside | App checks cache, then DB | App updates DB and invalidates cache | Simple and common | Staleness / miss complexity |
| Read-through | Cache loads on miss | Depends on implementation | Centralized loading | Less application control |
| Write-through | Cache writes DB synchronously | Cache and DB updated together | Fresh cache | Higher write latency |
| Write-behind | Cache persists asynchronously | Cache accepts write first | Fast writes | Data-loss / consistency risk |

---

# 10. TTL – Time to Live

TTL defines how long a cached value remains valid.

```text
product:123
TTL = 10 minutes
```

## Short TTL

Benefits:

- Less stale data

Costs:

- More cache misses
- More source-store load

## Long TTL

Benefits:

- Better hit rate
- Less database traffic

Costs:

- Stale data remains longer

## Choosing TTL

TTL should reflect:

- How frequently the data changes
- How damaging staleness would be
- How expensive the source query is
- Expected request volume

Examples:

```text
Static country list
→ long TTL

Product description
→ minutes / hours

Inventory preview
→ short TTL or event invalidation

Payment state
→ authoritative read
```

Avoid arbitrary TTLs without explaining why.

---

# 11. Cache Invalidation

Common approaches:

1. TTL expiration
2. Explicit invalidation
3. Event-driven invalidation
4. Versioned keys

## TTL Expiration

```text
Cache entry expires after N seconds
```

Simple, but stale data may remain until expiration.

## Explicit Invalidation

```text
Update Database
      ↓
Delete Cache Key
```

Trade-off: if invalidation fails, stale data may remain.

## Event-Driven Invalidation

```text
Database Update
      ↓
Event
      ↓
Cache Consumer
      ↓
Invalidate / Refresh
```

Useful when many services depend on the same data.

Trade-offs:

- Event delivery can lag
- Consumers may fail
- Cache becomes eventually consistent

## Versioned Cache Keys

```text
product:123:v1
product:123:v2
```

Useful for immutable or versioned objects.

Trade-off: old entries remain until expiration or eviction.

---

# 12. Cache Eviction

Caches have limited memory.

Common policies include:

- LRU – Least Recently Used
- LFU – Least Frequently Used
- FIFO – First In, First Out
- TTL-based expiration

### LRU

Remove the item that has not been used recently.

### LFU

Remove the least frequently accessed item.

### Interview Insight

You rarely need deep eviction-algorithm detail unless asked.

> "I would use an eviction policy such as LRU or LFU depending on whether recent or long-term popularity better predicts reuse."

---

# 13. Cache Hit Rate

```text
cache_hits
──────────────
total_requests
```

Example:

```text
95 hits
5 misses

Hit rate = 95%
```

A higher hit rate usually means:

- Lower database load
- Lower average latency

But hit rate alone does not prove the cache is useful.

Also consider:

- Memory cost
- Staleness
- Miss penalty
- Hot keys
- Invalidation complexity

---

# 14. Cache Stampede

A cache stampede occurs when many requests miss the same key simultaneously.

```text
Popular key expires
       ↓
10,000 requests arrive
       ↓
10,000 cache misses
       ↓
10,000 database queries
```

## Mitigations

### Request Coalescing / Single Flight

Only one request refreshes the key; others wait for the same result.

### Distributed Lock

One worker acquires a short lock and refreshes the value.

Trade-off: locks add timeout and failure complexity.

### TTL Jitter

```text
TTL = base TTL + small random amount
```

This prevents many keys from expiring at exactly the same time.

### Serve Stale While Refreshing

Return a recently expired value while one worker refreshes it.

Useful when slight staleness is acceptable.

---

# 15. Hot Keys

A hot key receives disproportionate traffic.

```text
cache:breaking_news
```

Millions of requests may hit one cache key, and therefore one cache node.

Possible mitigations:

- Replicate hot values
- Use local in-process caching
- Split or shard the representation
- Use CDN / edge caching for public content
- Rate limit abusive traffic

Important distinction:

```text
Hot partition
→ uneven traffic across partitions

Hot key
→ one specific key receives extreme traffic
```

---

# 16. Negative Caching

Repeated requests may ask for data that does not exist.

Without negative caching:

```text
Cache miss
→ DB lookup
→ Not found
```

every time.

Negative caching stores:

```text
user:999999 → NOT_FOUND
TTL = short duration
```

### Benefit

Protects the source database from repeated misses.

### Trade-off

If the object is created soon after, users may temporarily continue receiving "not found."

Use a short TTL.

---

# 17. Local Cache vs Distributed Cache

## Local / In-Process Cache

```text
App 1 → Local Cache
App 2 → Local Cache
App 3 → Local Cache
```

Benefits:

- Extremely low latency
- No network call

Trade-offs:

- Duplicate copies
- Harder invalidation
- Different instances may see different values
- Capacity tied to application memory

## Distributed Cache

```text
App 1 ─┐
App 2 ─┼──► Distributed Cache
App 3 ─┘
```

Benefits:

- Shared cached state
- Larger centralized capacity
- Easier cross-instance consistency

Trade-offs:

- Network latency
- Additional infrastructure
- Cache cluster can fail

## Hybrid

```text
Local Cache
    ↓ miss
Distributed Cache
    ↓ miss
Database
```

Faster, but more invalidation complexity.

---

# 18. Cache Failure

If the cache fails:

```text
Cache unavailable
       ↓
Requests fall back to database
```

The danger is that the database suddenly receives full traffic.

Protect the source using:

- Rate limiting
- Circuit breakers
- Request coalescing
- Graceful degradation
- Gradual cache warming
- Database capacity headroom

---

# 19. Cache Warming

After a cache restart:

```text
Empty Cache
    ↓
Huge DB traffic spike
```

Possible strategies:

- Preload popular keys
- Warm gradually
- Let organic traffic populate the cache
- Rate limit miss traffic

---

# 20. Cache Consistency

Whether stale data is acceptable depends on the operation.

## Product Page

A few seconds of stale price may be tolerable if checkout validates the authoritative value.

```text
Browse
→ cached price

Checkout
→ authoritative price
```

## Seat Booking

A stale cached seat status may show `AVAILABLE` after another user reserved it.

Use cache for browsing, but final reservation must use an atomic operation against the source of truth.

---

# 21. Cache and Source of Truth

Be explicit:

```text
Database
→ authoritative

Cache
→ derived / rebuildable
```

If the cache is lost:

```text
Repopulate from source
```

Critical correctness should not depend on a cache value unless the cache is intentionally acting as authoritative storage with appropriate durability guarantees.

---

# 22. Common Caching Mistakes

## Mistake 1 – Cache Everything

Caching adds:

- Memory cost
- Invalidation
- Staleness
- Operational complexity

Cache only data with a clear benefit.

## Mistake 2 – Use Cache to Hide a Bad Query

Before adding a cache:

```text
Check query
Check indexes
Check schema
```

## Mistake 3 – No Invalidation Strategy

If you cannot answer:

> "When does this cache entry become invalid?"

the design is incomplete.

## Mistake 4 – Treat Cache as Strongly Consistent

Critical decisions should validate against authoritative storage.

## Mistake 5 – Ignore Cache Failure

A cache outage can overload the source database.

## Mistake 6 – Same TTL Everywhere

TTL should reflect the business impact of staleness.

## Mistake 7 – Cache Unbounded Unique Keys

```text
Low reuse
+
high memory use
=
poor cache value
```

Cache data that is likely to be reused.

---

# 23. Worked Example – Product Catalog

Requirements:

- Product pages are read frequently
- Descriptions change rarely
- Price changes occasionally
- Inventory changes frequently
- Search may be slightly stale
- Checkout must be correct

## Product Description

```text
Cache key:
product:{product_id}

TTL:
Longer TTL, such as minutes or hours
```

Why:

- High read volume
- Low update frequency
- Slight staleness acceptable

## Price

Possible strategy:

```text
Short TTL
+
explicit invalidation on update
```

Checkout still verifies authoritative price.

## Inventory

Cache may support browsing:

```text
"Only 3 left"
```

But final checkout performs an authoritative inventory operation.

## Read Flow

```text
GET /products/123
        ↓
Cache
   │
   ├── Hit → Return
   └── Miss
        ↓
Source Store
        ↓
Populate Cache
        ↓
Return
```

## Write Flow

```text
Update Product
      ↓
Update Source of Truth
      ↓
Invalidate product:123
      ↓
Next read repopulates cache
```

Trade-offs:

- Cache invalidation complexity
- Short periods of stale data
- Multiple cache keys may represent the same entity
- Cache outage can increase source-store traffic

---

# 24. Worked Example – Social Feed

Popular posts may receive massive read traffic.

Possible cache:

```text
post:{post_id}
```

Caching individual posts may be easier than caching entire personalized feeds.

```text
Individual post
→ reused across many users

Entire personalized feed
→ unique per user
```

Prefer caching at the simplest reusable boundary.

---

# 25. Decision Framework

## Step 1 – Identify the Expensive Read

```text
What is slow or repeatedly requested?
```

## Step 2 – Identify the Source of Truth

```text
Where is authoritative data stored?
```

## Step 3 – Decide Whether Staleness Is Acceptable

```text
Seconds?
Minutes?
Never?
```

## Step 4 – Choose the Pattern

```text
Cache-aside?
Read-through?
Write-through?
Write-behind?
```

## Step 5 – Define Invalidation

```text
TTL?
Explicit delete?
Event-driven?
Versioned keys?
```

## Step 6 – Handle Failure

```text
Cache miss?
Cache outage?
Stampede?
Hot key?
```

## Step 7 – State the Trade-off

```text
What consistency or complexity is added?
```

---

# 26. Interview Language

Instead of:

> "I'll add Redis."

Say:

> "Product descriptions are read frequently and change infrequently, so I would cache them using cache-aside. The database remains authoritative, and I would invalidate the cache when the product changes, with a TTL as a safety net."

Instead of:

> "I'll cache inventory."

Say:

> "I may cache inventory for browsing, but checkout will validate and update inventory against the authoritative database because stale inventory can cause overselling."

Instead of:

> "The cache improves performance."

Say:

> "The cache removes repeated reads from the database and lowers latency, but it introduces staleness and invalidation complexity."

---

# 27. Interview Checklist

Before adding a cache, confirm:

- [ ] The expensive or repeated read is identified
- [ ] The source of truth is explicit
- [ ] Cache keys are defined
- [ ] The cache pattern is justified
- [ ] TTL is justified
- [ ] Invalidation strategy is defined
- [ ] Staleness tolerance is understood
- [ ] Cache misses are handled
- [ ] Cache failure behavior is defined
- [ ] Stampede risk is considered
- [ ] Hot keys are considered
- [ ] Critical writes verify against the authoritative store
- [ ] The cache provides enough reuse to justify its complexity

---

# 28. Thirty-Second Recap

- Cache repeated or expensive reads, not everything.
- The cache is usually derived; the database remains authoritative.
- Cache-aside is a common default.
- On a miss: read source → populate cache → return.
- On a write: update source → invalidate cache.
- TTL balances freshness against hit rate.
- Stale cache and cache miss are different problems.
- Cache stampedes happen when many requests miss the same key simultaneously.
- Hot keys create uneven cache-node traffic.
- Request coalescing, locks, TTL jitter, and stale-while-refresh can reduce stampedes.
- Cache outages can overload the source database.
- Critical operations such as payment, inventory, and seat ownership should verify authoritative state.

---

# 29. Practice Exercise

For each scenario, decide:

1. Whether caching is appropriate
2. What the cache key should be
3. Which pattern to use
4. TTL / invalidation strategy
5. Whether stale data is acceptable
6. One failure mode

### Scenario A

Product details are read millions of times and change a few times per day.

### Scenario B

User authorization permissions change rarely but incorrect permissions could expose private data.

### Scenario C

A celebrity post receives millions of views in minutes.

### Scenario D

Inventory counts change continuously during a flash sale.

### Scenario E

A search suggestion API repeatedly computes the same popular prefixes.

Use:

```text
Should cache:

Cache key:

Pattern:

TTL / invalidation:

Staleness tolerance:

Failure mode:
```

---

## 💡 Key Takeaway

> A cache is valuable only when the performance benefit is worth the consistency and invalidation complexity it introduces.
