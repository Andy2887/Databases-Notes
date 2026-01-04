# Transactions

## Overview

A **transaction** is a sequence of one or more database operations that are executed as a single logical unit of work. Transactions guarantee ACID properties to ensure data integrity and consistency even when multiple users access the database concurrently.

## Concurrency Issues

When multiple transactions execute concurrently without proper control, several problems can arise:

### Inconsistent Reads (Write-Read Conflict)

A transaction reads only part of what another transaction has updated, seeing the database in an intermediate state.

**Example:**
- User 1 updates Table 1 and then updates Table 2
- User 2 reads Table 2 (not yet updated by User 1) and then Table 1 (already updated by User 1)
- User 2 sees an inconsistent state of the database

### Lost Update (Write-Write Conflict)

Two transactions try to update the same record at the same time, causing one update to be overwritten.

**Example:**
- User 1 updates a toy's price to be `price × 2`
- User 2 updates the same toy's price to be `price + 5`, overwriting User 1's update
- User 1's update is lost

### Dirty Reads (Write-Read Conflict)

A transaction reads data that was modified by another transaction but never committed (and may be rolled back).

**Example:**
- User 1 updates a toy's price but the transaction gets aborted
- User 2 reads the updated price before User 1's transaction was rolled back
- User 2 has read invalid data

### Unrepeatable Reads (Read-Write Conflict)

A transaction reads the same record twice and gets different values because another transaction modified the record in between.

**Example:**
- User 1 reads a toy's price
- User 2 updates the toy's price to be `price × 2` and commits
- User 1 reads the toy's price again and gets a different value
- User 1 should see the same value within one transaction, so it must abort

## ACID Properties

Transactions guarantee the **ACID properties** to avoid the concurrency problems discussed above:

| Property | Description |
|----------|-------------|
| **Atomicity** | Either all operations in the transaction happen, or none happen. No partial completion. |
| **Consistency** | A transaction takes the database from one valid state to another, maintaining all constraints and rules (e.g., "the banking account balance column cannot be negative"). |
| **Isolation** | Each transaction executes as if it's the only one running. The DBMS may interleave operations from multiple transactions, but each transaction should not see intermediate states of others. |
| **Durability** | Once a transaction is committed, the changes are permanent. Even if the system crashes immediately after, the data persists when the system restarts. |

## Concurrency Control

**Concurrency control** mechanisms ensure that concurrent execution of transactions maintains database consistency and isolation.

### Serial Schedule

A **serial schedule** completes all operations of one transaction before beginning the operations of the next transaction.

**Example:**

![Serial Schedule](assets/p1.png)

### Equivalent Schedules

Two schedules are **equivalent** if they satisfy the following three conditions:

1. They involve the same transactions
2. Operations are ordered the same way within individual transactions
3. They each leave the database in the same final state

### Serializable Schedule

A schedule is **serializable** if its results are equivalent to some serial schedule. This ensures correctness even when operations are interleaved.

**Example:**

The schedule in this example is equivalent to the serial schedule above.

![Serializable Schedule](assets/p2.png)

### Conflicting Operations

Two operations **conflict** if they satisfy all three conditions:

1. The operations are from different transactions
2. Both operations operate on the same resource (e.g., same database record)
3. At least one operation is a write

### Conflict Equivalence

Two schedules are **conflict equivalent** if they order all pairs of conflicting operations in the same way.

### Conflict Serializability

A schedule is **conflict serializable** if it is conflict equivalent to some serial schedule. Conflict serializability is a sufficient condition for serializability and can be verified without executing the entire schedule.

## Conflict Dependency Graph

A **dependency graph** (also called a precedence graph) is used to determine whether a schedule is conflict serializable.

### Graph Structure

Dependency graphs have the following structure:

- **One node per transaction**
- **Edge from Ti to Tj** if both conditions are met:
  1. An operation Oi of Ti conflicts with an operation Oj of Tj
  2. Oi appears earlier in the schedule than Oj

### Serializability Test

A schedule is **conflict serializable if and only if its dependency graph is acyclic** (contains no cycles).

**Example 1: Conflict Serializable Schedule**

The dependency graph is acyclic, so this schedule is conflict serializable.

![Acyclic Dependency Graph](assets/p3.png)

**Example 2: Non-Conflict Serializable Schedule**

The dependency graph contains a cycle, so this schedule is not conflict serializable.

![Cyclic Dependency Graph](assets/p4.png)

## View Serializability

**View serializability** is an alternate way to determine overall serializability. View serializability will identify more serializable schedules than conflict serializability.

### View Equivalence

Two schedules S1 and S2 are **view equivalent** if they satisfy the following three conditions:

1. **Same initial reads**: The same transactions read the initial values of each data item
2. **Same dependent reads**: If Ti reads a value of X written by Tj in schedule S, it must read the same value written by Tj in S'
3. **Same final writes**: The same transactions perform the final write for each data item

### Blind Writes

The "secret sauce" of view serializability is how it handles **blind writes** (writing to a data item without reading it first).

Conflict serializability often flags schedules with blind writes as "non-serializable" because they create cycles in a precedence graph. However, view serializability recognizes that if a write is "blind" and then immediately overwritten by another "final write," the intermediate conflict doesn't actually change the final outcome of the database.

**Rule of Thumb:** Every conflict serializable schedule is view serializable, but not every view serializable schedule is conflict serializable.

## Lock

### Two Phase Locking

**Two phase locking (2PL)** is a scheme that ensures the database uses conflict serializable schedules. The two rules for 2PL are:

1. Transactions must acquire a **S (shared)** lock before reading, and an **X (exclusive)** lock before writing
2. Transactions cannot acquire new locks after releasing any lock

![p6](assets/p6.png)

The problem with this is that it does not prevent **cascading aborts**. For example:

1. T1 updates resource X and then releases the lock on X
2. T2 reads from X
3. T1 aborts
4. T2 must also abort because it read an uncommitted value of X

To solve this, we use **Strict Two Phase Locking (Strict 2PL)**. Strict 2PL is the same as 2PL, except all locks get released together when the transaction commits or aborts.

### Deadlock

#### Avoidance

One way we can get around deadlocks is by trying to avoid getting into a deadlock. We assign the transaction's priority by its age: `now - start time`. If T1 wants a lock that T2 holds, we have two options:

- **Wait-Die**: If T1 has higher priority, T1 waits for T2; else T1 aborts
- **Wound-Wait**: If T1 has higher priority, T2 aborts; else T1 waits

#### Detection

Use a **"waits-for" graph**. This graph will have one node per transaction and an edge from Ti to Tj if all conditions are met:

1. Tj holds a lock on resource X
2. Ti tries to acquire a lock on resource X, but Tj must release its lock on resource X before Ti can acquire its desired lock

![p10](assets/p10.png)

If a cycle is found, we will "shoot" a transaction in the cycle and abort it to break the cycle.

### Lock Granularity

#### Hierarchy of Granularity

Locking can happen at various levels within the database. Generally, the levels are structured as follows:

1. **Database Level**: The entire database is locked. Useful for maintenance or backups
2. **Table Level**: An entire table is locked. If User A is updating the "Employees" table, User B cannot even read it
3. **Page/Block Level**: A physical disk block (containing several rows) is locked
4. **Row (Tuple) Level**: Only a specific row is locked. This is the most common level for high-performance databases

#### Multiple Granularity Locking (MGL)

To manage these different levels efficiently, databases use **intent locks**. These signal that a transaction intends to lock something further down the hierarchy:

- **Intent Shared (IS)**: "I intend to place a Shared lock on a row inside this table"
- **Intent Exclusive (IX)**: "I intend to place an Exclusive lock on a row inside this table"
- **Shared Intent Exclusive (SIX)**: "I intend to place an Exclusive lock on a row inside this table, and none of you could modify anything in the table"

This prevents a conflict where User A tries to lock an entire table while User B is currently editing a single row inside it. Without intent locks, the database would have to check every single row to see if it's safe to lock the table.

#### Multiple Granularity Locking Protocol

The protocol follows these rules:

1. Each transaction starts from the root of the hierarchy
2. To get S or IS lock on a node, must hold IS or IX on parent node
3. To get X or IX on a node, must hold IX or SIX on parent node
4. Must release locks in bottom-up order
5. 2-phase and lock compatibility matrix rules enforced as well

The protocol is correct in that it is equivalent to directly setting locks at leaf levels of the hierarchy.