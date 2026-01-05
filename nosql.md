# NoSQL

## History

### OLTP and OLAP

**OLTP** and **OLAP** are two broad classes of workloads that can be subjected to a database.

**Online Transaction Processing (OLTP)** is a class of workloads characterized by high numbers of transactions executed by large numbers of users. This kind of workload is common to "frontend" applications such as social networks and online stores. Queries are typically simple lookups (e.g., "find user by ID", "get items in shopping cart") and rarely include joins. OLTP workloads also involve high numbers of updates (e.g., "post tweet", "add item to shopping cart"). Because of the need for consistency in these business-critical workloads, the queries, inserts and updates are performed as transactions.

**Online Analytical Processing (OLAP)** is a class of read-only workloads characterized by queries that typically touch a large amount of data. OLAP queries typically involve large numbers of joins and aggregations in order to support decision making (e.g., "sum revenues by store, region, clerk, product, date").

In many cases, OLTP and OLAP workloads are served by separate databases.

| Property | OLTP | OLAP |
|----------|------|------|
| **Main read pattern** | Small number of records per query, fetched by key | Aggregate over large number of records |
| **Main write pattern** | Random-access, low-latency writes from user input | Bulk import or event stream |
| **Primarily used by** | End user/customer, via web application | Internal analysis, for decision support |
| **What data represents** | Latest state of data (current point in time) | History of events that happened over time |
| **Dataset size** | Gigabytes to terabytes | Terabytes to petabytes |

### NoSQL: Scaling Databases for Web 2.0

In the early 2000s, web applications began to incorporate more user-generated content and interactions between users (called "Web 2.0"). Databases needed to handle the very large scale OLTP workload consisting of the tweets, posts, pokes, photos, videos, likes and upvotes being inserted and queried by millions or tens of millions of users simultaneously. Database designers began exploring how to meet this required scale by relaxing the guarantees and reducing the functionality provided by relational databases. The resulting databases, termed **NoSQL**, exhibit a simpler data model with restricted updates but can handle a higher volume of simple updates and queries.

The natural question to ask is: How does simplifying the data model and reducing the functionality of the database allow NoSQL to scale better than traditional relational databases? More specifically, why is it hard to scale relational databases?

### Why Relational Databases (SQL) are Hard to Scale

Traditional databases like MySQL or PostgreSQL were designed in an era where storage was expensive and data "truth" was paramount. This led to two major scaling roadblocks:

#### The "Join" Problem (Normalization)

Relational databases use normalization to eliminate redundancy. Information is split into many tables (e.g., Users, Orders, Products) and linked by keys.

**The Scaling Issue:** When you scale horizontally (sharding), you split your data across 100 different servers. If you need to perform a JOIN between Users and Orders, those two tables might live on different physical machines.

**The Result:** The database must coordinate a massive data transfer over the network to merge those rows. Network latency makes this incredibly slow, and the complexity grows exponentially as you add more nodes.

#### The ACID & Coordination Problem

RDBMS are built on ACID properties (Atomicity, Consistency, Isolation, Durability). If you transfer money from Account A to Account B, the database guarantees that both happen or neither happens.

**The Scaling Issue:** In a distributed system, maintaining a "single version of the truth" across 50 servers requires constant talking. Every write must be "locked" and "confirmed" by multiple nodes (often using protocols like Two-Phase Commit).

**The Result:** The more servers you add, the more time they spend talking to each other rather than processing data. This is why SQL databases usually scale vertically (buying a bigger, more expensive server) rather than horizontally.

### How NoSQL Simplification Solves This

NoSQL scales better because it "cheats" by changing the rules of how data is stored and managed.

#### Denormalization (Self-Contained Data)

Instead of splitting data into 10 tables, NoSQL often stores everything related to a single record in one "document" (like a JSON object).

**Scaling Advantage:** Since a "User" document contains their profile, settings, and recent orders all in one place, the database never needs to perform a cross-server JOIN.

**The Result:** A single server can satisfy the entire request without talking to any other node. This makes it trivial to split data (sharding) across thousands of cheap servers.

#### Relaxed Consistency (The CAP Theorem)

Most NoSQL databases follow the BASE model (Basically Available, Soft state, Eventual consistency) rather than ACID. They prioritize Availability and Partition Tolerance over immediate Consistency.

**Scaling Advantage:** When you write data to Node A, the database doesn't wait for Node B, C, and D to confirm they have it before telling the user "Success." It updates the others in the background (eventual consistency).

**The Result:** Writes are lightning-fast because there is no global "lock" or heavy coordination.

#### Reduced Functionality

By removing features like Triggers, Foreign Key Constraints, and Complex Aggregations, the database engine becomes "thinner."

**Scaling Advantage:** The CPU spends less time checking rules and more time moving bytes. Much of the "logic" is pushed to the application layer, allowing the database to focus purely on high-speed retrieval.

## Methods of Scaling Database

### Partitioning

Database can be **partitioned** into segments of data that are spread out over multiple machines. Performance can increase for two reasons:

1. Queries can be executed in parallel on multiple machines if they touch different parts of the database
2. Partitioning the database may allow each partition to fit into memory, which can reduce the disk I/O cost for executing queries

Partitioning can be effective for write-heavy workloads, as query write operations such as inserts and updates, will likely involve writing to just a single machine. However, read-heavy workloads may suffer when queries access data spanning multiple machines (e.g. consider a join on relations partitioned across many machines, requiring costly network transfer), which can decrease performance in comparison to the single database server model.

### Replication

**Replication** is motivated not only by the need to scale the database, but also for the database to be resilient to failures and extensive down-time. For each partition, there is a main/primary (formerly called master) copy and duplicates/replicas that are kept in sync.

This is effective for read-heavy workloads, as queries that read the same data can be executed in parallel on different replicas. However, as the number of replicas increases, writes become increasingly expensive, as queries that update data must now write to each replica of the data to keep them in sync.

## NoSQL Data Models

### Key-Value Stores

The **key-value store (KVS)** data model is extremely simple and consists only of (key, value) pairs to allow for flexibility.

**Examples:** AWS's DynamoDB, Facebook's RocksDB, and Memcached

### Wide-Column Stores

**Wide-column stores** is like a 2-dimensional key-value store, where the first key is a row identifier used to find a particular row, and the second key is used to extract the value of a particular column.

```
key = (rowID, columnID), value = field
```

In a traditional database, if you want to find the "Age" of a user, the system has to read the entire row (Name, Email, Address, Age, Bio) just to extract that one piece of information.

In a Wide-Column Store, because column families are stored together, the system can skip the "Address" and "Bio" columns entirely and only read the "Age" column across millions of records. This makes it incredibly fast for:

- Analytical queries (e.g., "What is the average age of our users?")
- Write-heavy workloads (e.g., logging millions of sensor readings per second)

**Examples:** Apache Cassandra, Apache HBase

### Document Stores

A **Document Store** (or document-oriented database) is a type of NoSQL database designed to store, retrieve, and manage semi-structured data. A document store treats each record as a self-contained unit called a **Document**, typically formatted as JSON, BSON, or XML.

**Examples:** MongoDB, CouchDB

### JSON vs Relational

First, relational databases trade flexibility for simplifying application code. Applications querying a relational database will know exactly the structure and data types of the rows that are returned; this can be determined through examining the schemas of the tables being queried. However, applications querying a document database will need to inspect the schema of each document in a collection, which pushes complexity into the application logic.

Second, the document model is more suitable for data which is primarily accessed by a primary key (like an email or user ID). For these kinds of lookups, the document model exhibits better **locality**; all the data related to the key of interest can be returned in a single query. This is typically more efficient for a document database than for a relational database: information could be stored in multiple tables and require several joins in order to retrieve the same information.
