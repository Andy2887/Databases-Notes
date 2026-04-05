# Databases Notes

## Overview

A collection of notes covering core database concepts and modern database systems analysis.

## Content

### Fundamentals

- [Storage](storage.md)
- [Query Execution](query_execution.md)
- [Query Optimizer](query_optimizer.md)
- [Transactions](transactions.md)

### Databases Analysis

- [Milvus](milvus.md) - An open-source vector database built for GenAI applications.
  - [Milvus: A Purpose-Built Vector Data Management System](https://www.cs.purdue.edu/homes/csjgwang/pubs/SIGMOD21_Milvus.pdf)

- [Snowflake](snowflake.md) - The pioneering cloud-native data warehouse in the commercial sphere.
  - [The Snowflake Elastic Data Warehouse](https://www.cs.cmu.edu/~15721-f24/papers/Snowflake.pdf)
- [Databricks](databricks.md) - A cloud-based data lakehouse for data analytics and AI.
  - [Lakehouse: A New Generation of Open Platforms that Unify Data Warehousing and Advanced Analytics](https://www.cidrdb.org/cidr2021/papers/cidr2021_paper17.pdf)
  - [Delta Lake: High-Performance ACID Table Storage over Cloud Object Stores](https://www.vldb.org/pvldb/vol13/p3411-armbrust.pdf)
  - [Photon: A Fast Query Engine for Lakehouse Systems](https://people.eecs.berkeley.edu/~matei/papers/2022/sigmod_photon.pdf)

### TODO

1. Add query optimizer.
1. Add Timestamp Ordering Concurrency Control and Multi-Version Concurrency Control
1. Add Database logging and Recovery.
1. Add databricks to database analysis.
1. Add distributed database, and concentrate all distributed related knowledge to this module. 

## Acknowledgement

These notes are made to help me keep track of what I learned from Intro to Databases and Advanced Database Systems at Northwestern.

I also learned a lot from [CMU Intro to Databases](https://15445.courses.cs.cmu.edu/fall2024/) and [CMU Advanced Database Systems](https://15721.courses.cs.cmu.edu/spring2023/). Special thanks to Professor Pavlo and CMU Database Group for making their learning materials open source.
