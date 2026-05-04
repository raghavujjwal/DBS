---
layout: default
title: Transaction Management & Concurrency
---

# Transaction Management & Concurrency Control

---

## What is a Transaction?

A **transaction** is a logical unit of database processing — one complete execution of a user program, which may include multiple read and write operations. The DBMS treats a transaction as an atomic unit: either all operations commit successfully, or none do.

### Basic Database Operations

| Operation | Steps Involved |
|-----------|---------------|
| `read_item(X)` | Find disk block containing X → copy block to buffer → copy X value to program variable |
| `write_item(X)` | Find disk block containing X → copy block to buffer → copy new value to buffer → write buffer to disk |

---

## ACID Properties

Every transaction must satisfy these four properties:

| Property | Meaning | Enforced By |
|----------|---------|------------|
| **Atomicity** | All operations complete, or none do (no partial execution) | Recovery subsystem |
| **Consistency** | Transaction takes DB from one valid state to another | The programmer |
| **Isolation** | Executing transactions appear independent — no partial effects visible | Concurrency control |
| **Durability** | Committed changes persist despite system crashes | Recovery subsystem |

---

## Transaction State Diagram

```
            ← partially committed →
Active ─────────────────────────────→ Committed
  │                                      
  ↓                                      
Failed ──────────────────────────────→ Aborted
```

| State | Meaning |
|-------|---------|
| **Active** | Transaction is currently executing |
| **Partially Committed** | Final operation executed; awaiting commit |
| **Committed** | Transaction successfully completed; changes permanent |
| **Failed** | Execution cannot continue due to error |
| **Aborted** | Transaction rolled back; DB restored to pre-transaction state |

**Terminated = Committed OR Aborted** (PYQ 23-24 Q5)

---

## Concurrency Problems

When multiple transactions execute concurrently without control, four problems arise:

### 1. Lost Update
```
T1: Read(X) = 100
T2: Read(X) = 100
T2: Write(X = 120)    ← T2's update
T1: Write(X = 150)    ← T1 overwrites T2's change — T2's update is LOST
```
Both read the same value; the last write wins, destroying the earlier update.

### 2. Dirty Read (Uncommitted Dependency)
```
T1: Write(X = 200)    ← T1 writes but hasn't committed
T2: Read(X) = 200     ← T2 reads T1's uncommitted value
T1: ABORT             ← T1 rolls back
```
T2 read a value that never officially existed — a "dirty" (uncommitted) value.

### 3. Inconsistent Analysis (Unrepeatable Read)
T2 reads aggregate (sum, count) while T1 is modifying the data. T2 reads some old values and some new values, producing an incorrect aggregate.

### 4. Deadlock
T1 waits for a lock held by T2; T2 waits for a lock held by T1. Neither can proceed.

---

## Serializability

A **schedule** is a sequence of operations from multiple concurrent transactions.

A schedule is **serializable** if its effect on the database is equivalent to some **serial schedule** (transactions executing one after another, with no interleaving).

### Conflicting Operations

Two operations **conflict** if ALL of the following hold:
1. They belong to **different transactions**
2. They access the **same data item**
3. **At least one** is a **write**

| Conflict Pair | Problem |
|--------------|---------|
| W(X) then R(X) by different T | Dirty read / T reads written value |
| R(X) then W(X) by different T | Unrepeatable read |
| W(X) then W(X) by different T | Lost update |

---

## Conflict Serializability — Precedence Graph

**Algorithm:**
1. Create one node per transaction
2. For each pair of conflicting operations where Ti's operation comes **before** Tj's in the schedule: add edge **Ti → Tj**
3. **Acyclic graph** → conflict serializable (topological sort gives equivalent serial order)
4. **Cycle** → NOT conflict serializable

### Worked Example

Schedule: R1(A), R2(A), W1(A), R3(B), R4(B), W2(A), W3(B), R1(B), W1(B)

Conflicts:
- R2(A) before W1(A): **T2 → T1**
- R1(A) before W2(A): **T1 → T2**

Edges T1→T2 and T2→T1 form a **cycle** → **NOT conflict serializable**.

---

## View Serializability

Schedules S and S' are **view equivalent** if:
1. **Same initial reads:** If Ti reads the initial value of X in S, Ti does the same in S'
2. **Same reads-from:** If Ti reads X written by Tj in S, same holds in S'
3. **Same final writes:** The transaction that performs the final write of X is the same in both

**View serializable:** View equivalent to some serial schedule.

> Every conflict serializable schedule is view serializable, but NOT vice versa.
> Testing view serializability is NP-complete — not practical for DBMS use.

---

## Recoverability

### Recoverable Schedule
If Ti reads data written by Tj, then **Tj must commit before Ti commits**.

If Ti has already committed but Tj later aborts, we cannot undo Ti's changes → **non-recoverable** (unacceptable).

### Cascadeless (Avoids Cascading Rollback)
Ti may only read X **after the transaction that last wrote X has committed**.

### Strict Schedule
Ti may neither read NOR write X until the last writer of X has committed or aborted.

```
Strict ⊂ Cascadeless ⊂ Recoverable ⊂ All schedules
```

### Checking Recoverability

For each reads-from relationship (Ti reads X after Tj wrote X):

| Property | Requirement |
|----------|-------------|
| Recoverable | commit(Tj) before commit(Ti) |
| Cascadeless | commit(Tj) before Ti reads X |
| Strict | commit/abort(Tj) before Ti reads OR writes X |

---

## Lock-Based Concurrency Control

### Lock Types

| Lock | Alias | Compatibility |
|------|-------|--------------|
| **Shared (S)** | Read lock | Multiple transactions can hold S on same item simultaneously |
| **Exclusive (X)** | Write lock | Only one transaction; incompatible with any other lock |

**Compatibility matrix:**

| | S | X |
|--|---|---|
| **S** | Yes | No |
| **X** | No | No |

**Lock upgrade:** A transaction holding S can upgrade to X if no other transaction holds S on the same item.

---

## Two-Phase Locking (2PL)

**Rule:** A transaction may NOT acquire any new lock after it has released any lock.

Two phases:
- **Growing phase:** Locks are acquired; none are released
- **Shrinking phase:** Locks are released; no new locks are acquired

**Guarantee:** 2PL ensures conflict serializability.

### 2PL Variants

| Variant | When X-locks Released | Guarantees |
|---------|----------------------|-----------|
| **Simple 2PL** | Anytime after growing phase ends | Conflict serializability |
| **Strict 2PL** | At commit or abort only | + Cascadelessness (no dirty reads) |
| **Rigorous 2PL** | ALL locks at commit/abort | + Recoverability |
| **Conservative 2PL** | Acquire ALL locks before start | + Deadlock freedom |

### Deadlock in 2PL

**Wait-for graph:** Node per transaction; edge Ti → Tj if Ti waits for a lock held by Tj.
**Deadlock:** Cycle in the wait-for graph.

**Detection:** Run cycle detection periodically; choose a victim transaction and abort it.

**Prevention (Conservative 2PL):** All locks acquired before execution starts. If any lock is unavailable, transaction waits without holding any locks → no circular waits possible.

### Lock Table Walkthrough Example

Schedule: R1(A), R2(A), W1(A), R2(B), W2(A)

| Step | Operation | Request | Status | Lock Table |
|------|-----------|---------|--------|-----------|
| 1 | R1(A) | T1: S(A) | Granted | A: {T1:S} |
| 2 | R2(A) | T2: S(A) | Granted | A: {T1:S, T2:S} |
| 3 | W1(A) | T1: upgrade S→X(A) | **Waits** (T2 holds S) | A: {T2:S}, T1 waiting |
| 4 | R2(B) | T2: S(B) | Granted | B: {T2:S} |
| 5 | W2(A) | T2: upgrade S→X(A) | **Waits** (T1 waiting for X) | Deadlock! |

T1 waits for T2 to release S(A); T2 waits for T1's pending X(A) upgrade → **deadlock**.

---

## Timestamp Ordering Protocol

Each transaction Ti assigned a **timestamp TS(Ti)** at start. Each data item X has:
- **R-TS(X):** largest TS among transactions that read X
- **W-TS(X):** largest TS among transactions that wrote X

### Read Rule

Ti wants to read X:
```
if TS(Ti) < W-TS(X):
    REJECT — Ti arrived too late; rollback Ti, restart with new TS
else:
    Allow read; R-TS(X) = max(R-TS(X), TS(Ti))
```

### Write Rule (with Thomas Write Rule)

Ti wants to write X:
```
if TS(Ti) < R-TS(X):
    REJECT — some later transaction already read X; rollback Ti
elif TS(Ti) < W-TS(X):
    SKIP write (Thomas Write Rule) — Ti's write is obsolete; don't reject, just ignore
else:
    Allow write; W-TS(X) = TS(Ti)
```

### Timestamp Protocol Properties

| Property | Value |
|----------|-------|
| Conflict serializable | Yes |
| Deadlock free | Yes (no waiting — transactions rollback instead) |
| Recoverable | No (Ti may commit before the Tj it read from) |
| Cascadeless | No |

---

## Protocol Comparison (PYQ Favourite)

| Property | Simple 2PL | Strict 2PL | Rigorous 2PL | Timestamp |
|----------|:----------:|:----------:|:------------:|:---------:|
| Conflict serializable | ✓ | ✓ | ✓ | ✓ |
| Deadlock free | ✗ | ✗ | ✗ | ✓ |
| Recoverable | ✗ | ✓ | ✓ | ✗ |
| Cascadeless | ✗ | ✓ | ✓ | ✗ |

---

## Commit Dependency

When Ti reads data **written by Tj** (before Tj commits), Ti has a **commit dependency** on Tj.

- Ti cannot commit until Tj commits
- If Tj aborts, Ti must also abort
- Prevents non-recoverable schedules

---

## Common Exam Patterns

### Factual Questions
- What are the two lock types and their compatibility? (S and X; S-S compatible, all others incompatible)
- State the 2PL rule. (No new lock can be acquired after any lock has been released)
- Which 2PL variant eliminates deadlocks? (Conservative 2PL — pre-declares all locks)
- Which concurrency property does timestamp protocol guarantee that 2PL does not? (Deadlock freedom)

### Conceptual Questions
- Why does Strict 2PL prevent dirty reads but Simple 2PL does not?
  (Strict holds X-locks until commit; no other transaction can read the value until it's committed)
- Why is recoverable a necessary condition for a correct schedule?
  (If Ti commits before Tj and Tj later aborts, we can't undo Ti's effects — data is permanently incorrect)
- Can a schedule be view serializable but not conflict serializable? Give an example.
  (Yes — a schedule with a "blind write" that sets the final value correctly can be view serializable but have a cycle in the precedence graph)

### Solving Questions

**Precedence graph construction:**
1. List all pairs of conflicting operations
2. For each conflict, draw edge from earlier transaction to later
3. Check for cycles

**Schedule classification:**
- Check conflict serializability (precedence graph)
- Check recoverability (for each reads-from, verify commit order)
- Check cascadelessness (reads only after writer commits)

**Timestamp protocol simulation:**
- Track R-TS and W-TS for each data item
- Process each operation in schedule order
- Apply read/write rules; note any rollbacks
