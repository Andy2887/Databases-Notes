# Snowflake

Snowflake represents the pioneering cloud-native OLAP database in the commercial sphere.

---

## Architecture

Snowflake uses a hybrid architecture of both shared-nothing and shared-disk.

- Shared-nothing: each VW has its own disk as cache
- Shared-disk: for separating storage with compute.

<img src="assets/snowflake_architecture.jpg" style="zoom:50%;" />

*Question: why not pure shared-nothing?*

*Answer: the pure shared-nothing architecture has an important drawback - it tightly couples compute with storage.*

1. *if the sets of node changes, large amount of data needs to be moved to new nodes.*
2. *when you're upgrading a node, you couldn't use its data.* 
3. *you couldn't use specialized nodes to do specific workloads (for example, high I/O bandwidth and light compute)*

### Database Storage

Snowflake supports the following kinds of data:

- *Structured data* — such as rows and columns in a table — follows a strict tabular schema.
- *Semi-structured data* — such as a JSON file or an XML file — has a flexible schema.
- *Unstructured data* — such as a document, image, or audio file — has no inherent schema.

### Compute

A *virtual warehouse* is a cluster of virtual machines in Snowflake. Each virtual warehouse is an independent compute cluster that doesn’t share compute resources with other virtual warehouses. For example, each individual query runs on exactly one VW. As a result, each virtual warehouse has no effect on the performance of other virtual warehouses.

Users never know how many EC2 instances make up the VW.

When there's a EC2 instance failure, the data stays in the cache, and Snowflake uses a LRU replacement policy to eventually replace the contents. This ensures better availability and simplifies the system.

To solve skew handling, worker nodes could help other worker nodes process files. This is called file stealing.

To improve cache hit rate, the query optimizer assigns input file sets to worker nodes using consistent hashing over file names. Therefore, the queries accessing the same table files will be done on the same worker node. 

<img src="assets/virtual_warehouse.jpg" style="zoom:50%;" />

### Cloud Services

This layer contains a collection of services that manage virtual warehouses, queries, transaction, and metadata about database schemas, usage statistics, etc. The metadata is stored in a transactional key-value store.



