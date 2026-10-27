# Chapter 1: SQL and NoSQL Databases: Characteristics, Design, and Trade-Offs

> **Book:** *Database Design and Modeling with PostgreSQL and MySQL*
> **Chapter summary.** Ends with a list of key terms and some notes on where the book oversimplifies.

**The main idea:** There are two broad families of databases. **SQL (relational)** databases put correctness and structure first. **NoSQL** databases put scale, flexibility and uptime first. Neither is better in general. Picking one means knowing what each gives up.

## Contents

1. [Databases and data models](#1-databases-and-data-models)
2. [The relational model (SQL databases)](#2-the-relational-model-sql-databases)
3. [NoSQL and the document model](#3-nosql-and-the-document-model)
4. [The CAP theorem](#4-the-cap-theorem)
5. [Transactions and concurrency in NoSQL: BASE vs ACID](#5-transactions-and-concurrency-in-nosql-base-vs-acid)
6. [Pros and cons of NoSQL](#6-pros-and-cons-of-nosql)
7. [Key terms to remember](#key-terms-to-remember)
8. [Where the book oversimplifies](#where-the-book-oversimplifies)

---

## 1. Databases and data models

- A **database** is an organized collection of data that you can store, retrieve, update and manage electronically.
- A **data model** is the blueprint for how that data is organized. There are two broad camps:
  - **Relational model (SQL):** tables of rows and columns, linked by keys, normalized to cut redundancy, queried with SQL, and protected by **ACID** transactions.
  - **NoSQL models:** document, key-value, column-family and graph. They trade some transaction and consistency guarantees for flexibility, scale and speed.

---

## 2. The relational model (SQL databases)

It is built on **set theory and logic**. Each table represents one **entity**.

| Concept | What it means | Example from the book |
|---|---|---|
| **Table / row / column** | A table is a 2D structure. A row is one record. A column is one attribute. | A `Customers` table with ID, name, email and date of birth |
| **Primary key** | Uniquely identifies each row and enforces *entity integrity* | `customer_id` in `Customers` |
| **Foreign key** | A column that points to another table's primary key and enforces *referential integrity* | `customer_id` in `Orders` points to `Customers` |
| **Normalization** | Store each fact in only one place, spread across several tables, to prevent anomalies | Normal forms 1NF → 2NF → 3NF and beyond |

**Normalization prevents:**

- **Update anomalies:** the same fact stored twice ends up with two different values.
- **Insertion anomalies:** you can't add some data because other data doesn't exist yet.

**SQL** is the standard language for retrieving, inserting, updating and deleting data. You describe *what* you want, and the database engine works out *how* to store and fetch it.

### ACID transactions (the main strength of SQL)

- **Atomicity:** a transaction is all or nothing. If any part fails, the whole thing rolls back.
- **Consistency:** every transaction moves the database from one valid state to another, and all constraints and rules hold.
- **Isolation:** transactions running at the same time don't see each other's half-finished work. Isolation comes in levels that trade safety for speed:
  - *Read Uncommitted:* fastest, but allows **dirty reads** (seeing changes that haven't been committed).
  - *Serializable:* strictest. It prevents dirty reads, non-repeatable reads and phantom reads, but costs performance.
- **Durability:** once a transaction commits, its changes survive crashes and power loss.

> **Takeaway:** SQL is the default choice when data anomalies are unacceptable.

---

## 3. NoSQL and the document model

**Why NoSQL exists:** web-scale applications and big data needed databases that could:

1. scale **horizontally** (add more machines),
2. handle **unstructured or semi-structured** data, and
3. stay fast across distributed systems.

**The document model** stores data as JSON-like documents that can nest arrays, fields and sub-objects. It has no fixed schema, so you can add fields without migrations. The cost is that keeping data consistent across documents becomes harder.

### The four NoSQL types

#### 1. Key-value stores

The simplest type. They work like a giant dictionary or hash table.

- Very fast lookups by key, scale horizontally, and can live in memory or on disk.
- **Redis** (in-memory): caching sessions and query results, real-time game leaderboards.
- **Amazon DynamoDB** (managed key-value and document store): session storage, user preferences and personalization.

#### 2. Document stores

Store self-contained JSON or BSON documents.

- **Schemaless:** documents in the same collection can have different fields.
- **Self-contained:** related data is nested together, which means fewer joins and faster reads.
- **MongoDB** (BSON, rich query language and aggregations): content management systems, e-commerce product catalogs with product variants nested inside each product.
- **Couchbase** (JSON, a hybrid of key-value and document): user profiles, real-time analytics and caching.

#### 3. Column-family (wide-column) stores

Group related columns into families. A row can hold many columns, and different rows can have different columns.

- Distributed, with a partly flexible schema. Good for analytics and time-series data.
- **ClickHouse:** real-time analytics. It reads directly from **Kafka**, replicates for fault tolerance, and shards with TTL-based auto-purging.
- **Apache Cassandra:** IoT sensor time-series data, recommendation systems.
- **HBase** (runs on Hadoop's HDFS): clickstream analytics, social media profiles and posts at billions-of-users scale.

#### 4. Graph databases

**Nodes** are entities and **edges** are relationships. Both can carry properties, and edges can point one way or both ways.

- Very good at **traversing** relationships, for example "find all friends of X".
- **Neo4j** (query language: **Cypher**): social networks, recommendation engines, fraud detection.
- **Amazon Neptune** (managed): knowledge graphs, drug discovery (protein and molecule interactions), network analysis.

---

## 4. The CAP theorem

*Proposed by Eric Brewer in the early 2000s.*

A distributed system **cannot guarantee all three** of these at once:

- **C – Consistency:** every node returns the same, most recent data. Strong consistency means the system is *linearizable*, but it usually needs synchronous coordination between nodes, which adds latency.
- **A – Availability:** every request gets a response (data or an error), even when some parts have failed. This is usually achieved through replication and redundancy.
- **P – Partition tolerance:** the system keeps working when the network splits nodes apart.

### The three combinations and when to use each

| Choice | Behavior | Good fit for |
|---|---|---|
| **CA** | Consistent and available, but blocks updates during a partition. The book says traditional SQL is here. | Banking and finance, reservation systems |
| **CP** | Stays consistent and may become unavailable during a partition | Configuration management, some e-commerce |
| **AP** | Stays available and becomes **eventually consistent**. Most NoSQL databases are here. | Social media, CDNs, low-latency applications |

---

## 5. Transactions and concurrency in NoSQL: BASE vs ACID

NoSQL usually follows **BASE** instead of ACID:

- **Basically Available:** the system keeps answering, even through failures.
- **Soft state:** replicas can disagree for a while.
- **Eventually consistent:** if no new writes come in, all replicas eventually match.

**Why NoSQL chooses BASE:**

1. high availability,
2. horizontal scalability (strong consistency becomes a bottleneck at scale), and
3. its varied data models need different consistency rules.

**What this means for developers:**

- **Your application has to handle temporarily stale or conflicting data.**
- **Optimistic concurrency control:** no locks up front. Conflicts are detected and resolved at commit time.
- **Conflict resolution:** strategies such as **last-write-wins** or **vector clocks** reconcile replicas that disagree.

---

## 6. Pros and cons of NoSQL

### Advantages

- **Scalability:** scales horizontally on commodity hardware. You add servers as data grows.
- **Flexibility:** no fixed schema, so new fields need no migrations or downtime. This helps fast-moving projects.
- **High availability:** replication across nodes removes single points of failure.
- **Performance:** no complex joins, which means high throughput and low latency. Good for real-time analytics and large user bases.

### Disadvantages

- **Lack of standardization:** each database has its own data model and query language. That means more to learn, and moving data between systems is harder.
- **Limited transaction support:** BASE is a poor fit for systems that need strict integrity, such as finance or complex workflows.
- **Maturity and ecosystem:** some NoSQL databases have less tooling, documentation and community support than mature SQL systems.

---

## Key terms to remember

- Primary key / foreign key
- Normalization (1NF / 2NF / 3NF)
- Update and insertion anomalies
- ACID
- Isolation levels
- Dirty, non-repeatable and phantom reads
- Document, key-value, column-family and graph stores
- CAP (CA / CP / AP)
- BASE
- Eventual consistency
- Optimistic concurrency control
- Last-write-wins
- Vector clocks

---

## Where the book oversimplifies

These points are worth knowing if you go on to interviews or real system design:

1. **"CA" isn't really an option for a distributed system.** Network partitions will happen, so in practice the choice is between **CP and AP** *when a partition occurs*. A "CA" database is really a single-node system. The book also says NoSQL "prioritizes either availability or partition tolerance", which frames it wrongly: P is required, and the real trade-off is C against A.
2. **A foreign key on its own models one-to-many.** Many-to-many relationships need a **junction table** that holds two foreign keys.
3. **ClickHouse is a different kind of database from Cassandra and HBase.** ClickHouse is a columnar *analytical (OLAP)* database that you query with SQL. Cassandra and HBase are *wide-column* stores built for operational workloads. Both are called "column" databases, but they work very differently.
4. **"No transactions" in NoSQL is no longer strictly true.** MongoDB has supported multi-document ACID transactions since version 4.0, and DynamoDB has transactions too. BASE describes the *default* behavior, not the only one.
