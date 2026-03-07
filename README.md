# Databases Notes

## Overview

A collection of notes covering core database concepts, including storage systems, query execution, transaction management, and NoSQL databases. These notes are useful for understanding how modern database systems work under the hood.

## Content

### Fundamentals

- [Storage](storage.md) - File types (heap and sorted files), record types, buffer management with LRU/Clock/MRU policies, and storage models (NSM, DSM, PAX, HTAP).
- [Query Execution](query_execution.md) - Query planner processing models (iterator, materialization, vectorization) and parallel query processing (inter-operator and intra-operator parallelism).
- [Transactions](transactions.md) - Concurrency issues, ACID properties, concurrency control, two-phase locking, deadlock handling, lock granularity, and distributed transactions with two-phase commit (2PC).

### Databases Analysis

- [Milvus](milvus.md) - An open-source vector database built for GenAI applications.
  - Paper: [Milvus - A Purpose-Built Vector Data Management System](<papers/Milvus - A Purpose-Built Vector Data Management System.pdf>)

- [Snowflake](snowflake.md) - The pioneering cloud-native OLAP database in the commercial sphere.
    - Paper: [The Snowflake Elastic Data Warehouse](<papers/The Snowflake Elastic Data Warehouse.pdf>)

### TODO

1. Add query optimizer.

## Acknowledgement

I learned a lot from [CMU Intro to Databases](https://15445.courses.cs.cmu.edu/fall2024/) and [CMU Advanced Database Systems](https://15721.courses.cs.cmu.edu/spring2023/). Special thanks to Professor Pavlo and CMU Database Group for making their learning materials open source.
