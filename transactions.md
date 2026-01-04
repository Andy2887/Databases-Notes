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



