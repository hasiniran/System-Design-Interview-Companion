# Workbook 04 – Sample Answers

> These are **sample answers**, not the only correct answers.
>
> Storage decisions depend on requirements, scale, access patterns, consistency needs, and operational constraints. A different answer may be equally strong if the reasoning is explicit and the trade-offs are understood.

---

# Exercise 1 – Identify the Data

## Google Drive

| Data Type     | Example                                 |
| ------------- | --------------------------------------- |
| File contents | Documents, images, videos, PDFs         |
| File metadata | Name, size, MIME type, timestamps       |
| User accounts | Identity, profile, account status       |
| Permissions   | Owner, viewer, editor access            |
| Sharing data  | Sharing links, expiration, access scope |

### Other Reasonable Answers

* Folder hierarchy
* Version history
* Collaboration state
* Search index
* Audit logs

---

## Instagram

| Data Type            | Example                         |
| -------------------- | ------------------------------- |
| User accounts        | Profile and account information |
| Media                | Photos and videos               |
| Post metadata        | Caption, timestamp, owner       |
| Social relationships | Followers and following         |
| Engagement data      | Likes and comments              |

### Other Reasonable Answers

* Feed data
* Stories
* Search index
* Notifications
* Direct messages

---

## Food Delivery Platform

| Data Type       | Example                                 |
| --------------- | --------------------------------------- |
| User accounts   | Customers, drivers, restaurant users    |
| Restaurant data | Restaurant profile and operating hours  |
| Menu data       | Items, prices, availability             |
| Orders          | Items, totals, payment and order status |
| Driver location | Time-based geographic updates           |

### Other Reasonable Answers

* Payment records
* Delivery assignments
* Reviews
* Promotions
* Search index

---

## Sample Reflection

An Instagram-like system stores data with very different characteristics.

Photos and videos are large binary objects, while users and post metadata are structured. Following relationships may require graph-like access patterns, search requires specialized indexing, and popular feed data may benefit from caching.

Using one storage system for everything can create inefficient access patterns and force one technology to solve problems it was not designed for.

---

# Exercise 2 – Data Characteristics

| Data                     | Sample Characteristics                                     |
| ------------------------ | ---------------------------------------------------------- |
| User accounts            | Structured, relational, transactional                      |
| Order records            | Structured, relational, transactional                      |
| Uploaded videos          | Large binary objects, durable, long-term retention         |
| Product catalog          | Structured or semi-structured, potentially flexible schema |
| User sessions            | Key-based access, temporary, frequently accessed           |
| File metadata            | Structured, queryable, relational                          |
| Application metrics      | Time-series, append-heavy                                  |
| Article search index     | Searchable text, relevance-ranked                          |
| Social graph             | Highly connected relationships, traversal-heavy            |
| Historical sales reports | Analytical, historical, aggregation-heavy                  |

---

## Sample Reflection

**Order records**

The strongest influence is the need for transactional correctness.

An order may involve status changes, inventory updates, payment references, and relationships between customers and order items. These requirements strongly favor transactional storage.

---

# Exercise 3 – Storage Category Selection

| Requirement                                       | Sample Storage Category | Why?                                                        |
| ------------------------------------------------- | ----------------------- | ----------------------------------------------------------- |
| Bank transfers require ACID transactions          | Relational Database     | Transactions and consistency are primary requirements.      |
| Users upload large videos                         | Object Storage          | Designed for durable, scalable binary-object storage.       |
| Product attributes vary significantly by category | Document Database       | Flexible schemas can model varying attributes naturally.    |
| Session data is accessed by session ID            | Key-Value Store         | Known-key access maps directly to simple key-based lookups. |
| Users search millions of articles by keywords     | Search Engine           | Supports full-text indexing, relevance, and ranking.        |
| Billions of events arrive continuously            | Wide-Column Database    | Can support distributed, high-throughput write workloads.   |
| CPU metrics are queried by time range             | Time-Series Database    | Optimized for timestamped data and time-window queries.     |
| Analysts run historical sales reports             | Data Warehouse          | Optimized for analytical scans and aggregations.            |
| Friend-of-friend traversal is common              | Graph Database          | Relationship traversal is the dominant access pattern.      |
| Popular profiles are repeatedly requested         | Cache                   | Reduces latency and repeated source-of-truth reads.         |

---

## Least Obvious Decision – Sample Answer

**Billions of continuously arriving events**

A wide-column database may be appropriate when sustained distributed write throughput is the primary requirement, but the final decision depends on:

* Query patterns
* Retention
* Ordering requirements
* Consistency requirements
* Whether the events are primarily streamed, archived, or queried

### Useful Clarifying Question

> How will these events be queried after ingestion?

The answer could change the storage decision.

---

# Exercise 4 – Polyglot Persistence

## Instagram Storage Design

| Data                          | Data Characteristics                              | Sample Storage Category                                 | Why?                                                                    | Trade-off                                                                                |
| ----------------------------- | ------------------------------------------------- | ------------------------------------------------------- | ----------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| User accounts                 | Structured, relational, transactional             | Relational Database                                     | Supports account relationships, constraints, and transactional updates. | Horizontal scaling can require additional design.                                        |
| Photos                        | Large binary objects, durable                     | Object Storage                                          | Scales efficiently for large media objects.                             | Not suitable for rich metadata queries.                                                  |
| Photo metadata                | Structured, queryable, relational                 | Relational Database                                     | Supports ownership, timestamps, captions, and indexed lookups.          | Schema evolution requires management.                                                    |
| Likes                         | High-volume relationships, simple access patterns | Relational or Distributed Key-Value / Wide-Column Store | Choice depends on scale and query patterns.                             | Distributed options can complicate consistency and querying.                             |
| Comments                      | Structured, ordered, linked to users and posts    | Relational Database                                     | Natural relationships and query patterns.                               | Very high scale may require partitioning.                                                |
| Following relationships       | Relationship-heavy                                | Relational Database or Graph Database                   | Choice depends on traversal complexity and scale.                       | Graph storage adds specialization; relational storage may make deep traversal expensive. |
| Search index                  | Searchable text                                   | Search Engine                                           | Supports full-text search and relevance ranking.                        | Index is typically derived and may lag behind source data.                               |
| Frequently accessed feed data | Read-heavy, latency-sensitive                     | Cache                                                   | Reduces repeated computation and source-store reads.                    | Invalidation and stale data become concerns.                                             |

---

## Important Note on Multiple Reasonable Answers

Some rows intentionally allow more than one strong answer.

For example:

### Likes

A relational database may be reasonable when:

* Scale is moderate
* Transactions matter
* Queries are straightforward

A distributed key-value or wide-column store may be reasonable when:

* Engagement volume is enormous
* Access patterns are simple
* Horizontal write scaling dominates

The interview skill is not naming one universally correct database.

The skill is explaining what requirement makes one choice preferable.

---

## Architecture Summary

### Primary Sources of Truth

Possible answers:

* User accounts
* Post metadata
* Comments
* Canonical like state
* Canonical following relationships

These should live in durable persistent storage.

---

### Derived or Rebuildable Data

Possible answers:

* Search index
* Cached feed entries
* Recommendation features
* Aggregated counters
* Some notification views

These may be reconstructed from authoritative data or event streams.

---

### Cached Data

Possible answers:

* Popular profiles
* Popular posts
* Feed pages
* Frequently requested metadata
* Computed counters

---

## Biggest Storage Constraint

### Sample Answer: Storage Scale

Media dominates storage volume because photos and videos are retained long-term.

This pushes the architecture toward:

* Object storage for media
* Separate metadata storage
* CDN delivery
* Lifecycle and retention policies

### Alternative Strong Answer: Read Latency

If the interview scope focuses on home-feed delivery, read latency may be the dominant constraint.

A different answer is valid when the reasoning follows the stated requirements.

---

# Quick Fire

| Observation                   | Sample Storage Category                        |
| ----------------------------- | ---------------------------------------------- |
| ACID transactions             | Relational Database                            |
| Strong relationships          | Relational Database                            |
| Large binary files            | Object Storage                                 |
| Flexible JSON documents       | Document Database                              |
| Lookup by known key           | Key-Value Store                                |
| Frequently accessed data      | Cache                                          |
| Full-text keyword search      | Search Engine                                  |
| Historical analytics          | Data Warehouse                                 |
| Massive write throughput      | Wide-Column / Partitioned Distributed Database |
| Metrics over time             | Time-Series Database                           |
| Relationship traversal        | Graph Database                                 |
| Long-term file retention      | Object Storage                                 |
| Structured metadata           | Relational Database                            |
| Temporary session data        | Key-Value Store / Cache                        |
| Business intelligence reports | Data Warehouse                                 |

---

# 🧩 Observation → Requirement → Storage Decision

## User Accounts

> User accounts have a stable schema, strong relationships, and transactional updates.

Therefore, I need:

> structured storage with transactional guarantees and relationship support

So I will choose:

> a relational database

---

## Multi-Gigabyte Videos

> Users upload multi-gigabyte videos.

Therefore, I need:

> durable storage that scales efficiently for large binary objects

So I will choose:

> object storage

---

## Variable Product Attributes

> Product records contain different attributes depending on category.

Therefore, I need:

> flexible schema support

So I will choose:

> a document database

### Important Qualification

A relational database with JSON support may also be reasonable.

The decision depends on:

* Query patterns
* Transaction requirements
* Relationship complexity
* Degree of schema variability

---

## Session Data

> Sessions are retrieved using a known session ID and require very low latency.

Therefore, I need:

> fast key-based access

So I will choose:

> a key-value store

A cache may also be appropriate depending on durability requirements.

---

## Document Search

> Users search millions of documents by keywords and relevance.

Therefore, I need:

> full-text indexing and relevance ranking

So I will choose:

> a search engine

---

## Historical Analytics

> Analysts run aggregations over years of historical data.

Therefore, I need:

> storage optimized for large analytical scans and aggregations

So I will choose:

> a data warehouse

---

# Exercise 5 – Interview Challenge

Because the learner chooses one of three systems, there is no single answer key.

The following are sample directions.

---

## Option A – YouTube

### Major Data Types

| Data                         | Sample Storage                    |
| ---------------------------- | --------------------------------- |
| Video files                  | Object Storage                    |
| Video metadata               | Relational Database               |
| User accounts                | Relational Database               |
| Comments                     | Relational Database               |
| Search index                 | Search Engine                     |
| Frequently accessed metadata | Cache                             |
| Analytical viewing data      | Analytical Store / Data Warehouse |

### Likely Biggest Constraint

Storage scale and global media delivery.

### Key Trade-offs

* Multiple specialized stores increase operational complexity.
* Search indexes may be eventually consistent.
* Cached metadata may become stale.

---

## Option B – Dropbox

### Major Data Types

| Data                         | Sample Storage                                                    |
| ---------------------------- | ----------------------------------------------------------------- |
| File contents                | Object Storage                                                    |
| File metadata                | Relational Database                                               |
| Users                        | Relational Database                                               |
| Permissions                  | Relational Database                                               |
| Sharing links                | Relational Database / Key-Value Store depending on access pattern |
| Frequently accessed metadata | Cache                                                             |
| Search index                 | Search Engine                                                     |

### Likely Biggest Constraint

Durable storage growth.

### Key Trade-offs

* Metadata and object contents must remain logically synchronized.
* Caching introduces invalidation concerns.
* Search indexing may lag behind metadata updates.

---

## Option C – E-commerce Platform

### Major Data Types

| Data                         | Sample Storage                  |
| ---------------------------- | ------------------------------- |
| Users                        | Relational Database             |
| Orders                       | Relational Database             |
| Payments                     | Relational Database             |
| Product catalog              | Relational or Document Database |
| Product images               | Object Storage                  |
| Sessions                     | Key-Value Store                 |
| Search index                 | Search Engine                   |
| Frequently accessed products | Cache                           |
| Historical analytics         | Data Warehouse                  |

### Likely Biggest Constraint

Transactional correctness for orders and payments, or read scale for product browsing, depending on scope.

### Key Trade-offs

* Multiple storage systems increase complexity.
* Search indexes may be stale.
* Caches require invalidation.
* Flexible product schemas can complicate relational modeling.

---

# Sample Storage Decision Log

| Observation          | Requirement                    | Storage Decision    | Trade-off                     |
| -------------------- | ------------------------------ | ------------------- | ----------------------------- |
| Large binary media   | Massive durable object storage | Object Storage      | Weak querying capabilities    |
| Transactional orders | ACID guarantees                | Relational Database | Horizontal scaling complexity |
| Repeated reads       | Low latency                    | Cache               | Invalidation complexity       |
| Keyword search       | Full-text ranking              | Search Engine       | Derived index may lag         |
| Historical reporting | Analytical aggregations        | Data Warehouse      | Not suitable for OLTP         |

---

# 💭 Sample Reflection

## Which data type was hardest to place?

Following relationships or engagement data can be difficult because the best choice depends heavily on scale and access patterns.

---

## Which storage decision influenced the architecture the most?

Separating large binary objects from metadata often has the greatest impact because it creates distinct storage and access paths.

---

## Where might more than one storage category be reasonable?

Product catalogs are a common example.

A relational database may work when:

* Transactions matter
* Relationships are important
* Schema variability is manageable

A document database may work when:

* Attributes vary significantly
* Documents are usually retrieved as aggregates
* Cross-document relationships are limited

---

## Which trade-off deserves the most attention?

Polyglot persistence improves fit-for-purpose storage but increases:

* Operational complexity
* Monitoring requirements
* Data synchronization challenges
* Failure modes
* Team knowledge requirements

---

## What should I ask the interviewer?

Useful questions include:

* What are the dominant access patterns?
* Which data requires strong consistency?
* What is the expected scale?
* How frequently does the schema change?
* Is full-text search required?
* What data can be eventually consistent?
* What are the retention requirements?

---

# Common Review Notes

A strong answer should:

* Begin with data types.
* Identify relevant data characteristics.
* Consider access patterns and guarantees.
* Choose a storage category before naming a product.
* Separate large objects from metadata where appropriate.
* Distinguish source-of-truth data from derived data.
* Explain at least one trade-off.

A weak answer often sounds like:

> "I'll use PostgreSQL, Redis, and Kafka."

A stronger answer sounds like:

> "Order data is structured and transactional, so I need a relational database. Product images are large binary objects, so I'll store them separately in object storage. Popular product data is read frequently, so I'll cache it to reduce latency and source-database load."

---

# 🎯 Key Lessons

* Storage selection begins with understanding the data.
* Data characteristics, access patterns, and guarantees all matter.
* Different data types often justify different storage categories.
* There is rarely one universally correct storage technology.
* Strong answers connect **Observation → Requirement → Decision → Trade-off**.
* Product names are implementation choices; storage categories are architectural choices.
