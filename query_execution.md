# Query Execution

---

## Query Planner

A **query planner** is the component of a DBMS that determines the most efficient way to execute a SQL statement.

Example Query:

<img src="/Users/yuanliheng/Desktop/Database Notes/assets/example_query.jpg" style="zoom:50%;" />

### Processing Models

A DBMS's processing model defines how the system executes a query plan.

**Approach #1: Iterator Model**

Every operator implements a `next()` function. The root operator calls `next()` on its child, which calls `next()` on *its* child, all the way down to the leaf (table scan). **Each `next()` call returns one tuple at a time.**

**Example:**

1. The projection on the root calls `next()` on join.
2. Join recursively calls `next()` on R to build hash table, which **gets one tuple at a time**.
3. After building the table, Join calls `next()` on selection operator.
4. The selection operator calls `next()` on S, and returns **one qualifying tuple** up to hash join.
5. Hash join returns **one qualifying tuple** up to projection.
6. Step 1 and 3-5 is repeated until root's `next()` returns None. (Hash table is only built once)

Pros: it's simple.

Cons: **a lot of function call overhead.**

---

**Approach #2: Materialization Model**

Each operator processes its *entire* input and produces its *entire* output before passing it to the parent. **You call `Output()` and get back all the tuples at once.**

**Example:**

1. The projection on the root calls `output()` on join.
2. Join calls `output()` on R to build hash table. R returns **all of its tuples** to hash table.
3. After building the table, Join calls `output()` on selection operator.
4. The selection operator calls `output()` on S which gets **all of S**, and push **all qualifying tuples** up to hash join.
5. Hash join push qualifying tuples up to projection.

Pros: fewer function calls overhead

Cons: **enormous intermediate results in memory**

---

**Approach #3: Vectorization Model**

It works like the iterator model — operators have a `next()` function — but instead of returning one tuple, they return a **batch** of tuples (say, 1024 at a time).

---

## Parallel Query Processing

### Inter-operator Parallelism

**Definition:** making a query run as fast as possible by running the **operators** in parallel.

**Pipeline Parallelism**

In pipeline parallelism records are passed to the parent operator as soon as they are done. The parent operator can work on a record that its child has already processed while the child operator is working on a different record.

Example:

![pipeline](/Users/yuanliheng/Desktop/Database Notes/assets/pipeline.png)

In the above example, the project and filter can run at the same time because as soon as filter finishes a record, project can operate on it while filter picks up a new record to operate on.

**Bushy Tree Parallelism**

Different branches of the tree are run in parallel.

Example:

![bushytree](/Users/yuanliheng/Desktop/Database Notes/assets/bushytree.png)

In the above example, the left branch and right branch can execute at the same time.

### Intra-operator Parallelism

Operators are decomposed into independent instances that perform the **same function** on **different subsets of data**.

*Analogy: think about multiple threads each processing part of the data.*

Example: In the example below, each core works on 1/3 of the data.

<img src="assets/intra-operator parallelism.jpg" style="zoom:50%;" />

