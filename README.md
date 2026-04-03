# Databases Notes

## Overview

A collection of notes covering core database concepts and modern database systems analysis.

## Content

### Fundamentals

- [Storage](storage.md) - Record types, buffer management with LRU/Clock/MRU policies, and storage models (NSM, DSM, PAX, HTAP).
- [Query Execution](query_execution.md) - Query processing models and parallel query processing. 
- [Query Optimizer](query_optimizer.md) - (Working on it)
- [Transactions](transactions.md) - Concurrency control, two-phase locking, deadlock handling, lock granularity, and distributed transactions with two-phase commit (2PC).

### Databases Analysis

- [Milvus](milvus.md) - An open-source vector database built for GenAI applications.
  - Paper: [Milvus - A Purpose-Built Vector Data Management System](https://www.cs.purdue.edu/homes/csjgwang/pubs/SIGMOD21_Milvus.pdf)

- [Snowflake](snowflake.md) - The pioneering cloud-native OLAP database in the commercial sphere.
  - Paper: [The Snowflake Elastic Data Warehouse](https://www.cs.cmu.edu/~15721-f24/papers/Snowflake.pdf)

### TODO

1. Add query optimizer.
1. Add Timestamp Ordering Concurrency Control and Multi-Version Concurrency Control
1. Add Database logging and Recovery.
1. Add databricks to database analysis.
1. Add distributed database, and concentrate all distributed related knowledge to this module. 

## Acknowledgement

These notes are made to help me keep track of what I learned from Intro to Databases and Advanced Database Systems at Northwestern.

I also learned a lot from [CMU Intro to Databases](https://15445.courses.cs.cmu.edu/fall2024/) and [CMU Advanced Database Systems](https://15721.courses.cs.cmu.edu/spring2023/). Special thanks to Professor Pavlo and CMU Database Group for making their learning materials open source.
