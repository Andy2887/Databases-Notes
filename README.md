# Database Notes

## Overview

A collection of notes covering core database concepts, including storage systems, query execution, transaction management, and NoSQL databases. These notes are useful for understanding how modern database systems work under the hood.

## Content

### Fundamentals

- [Storage](storage.md) - File types (heap and sorted files), record types, buffer management with LRU/Clock/MRU policies, and storage models (NSM, DSM, PAX, HTAP).

- [Query Execution](query_execution.md) - Query planner processing models (iterator, materialization, vectorization) and parallel query processing (inter-operator and intra-operator parallelism).

- [Transactions](transactions.md) - Concurrency issues, ACID properties, concurrency control, two-phase locking, deadlock handling, lock granularity, and distributed transactions with two-phase commit (2PC).

### Special Topics

- [NoSQL](nosql.md) - History of OLTP/OLAP workloads, why relational databases are hard to scale, and NoSQL data models (key-value stores, wide-column stores, document stores).
