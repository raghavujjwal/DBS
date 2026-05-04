---
layout: default
title: Crash Recovery
---

# Crash Recovery

---

## Why Crash Recovery?

System crashes can leave the database in an inconsistent state because a transaction may be partially executed at the time of failure. Crash recovery ensures:
- **Atomicity:** Partial transactions are undone
- **Durability:** Committed changes are not lost

**Types of failures:**
- **System crash:** Main memory (volatile) is lost; disk storage survives
- **Disk failure:** Non-volatile storage is lost or corrupted (requires backup/archiving)

---

## Log-Based Recovery

A **log** (journal) on stable storage records every database modification before it happens.

### Log Record Format

| Record | Meaning |
|--------|---------|
| `[START Ti]` | Transaction Ti has begun |
| `[Ti, X, old_val, new_val]` | Ti updated item X from old_val to new_val |
| `[COMMIT Ti]` | Ti has successfully committed |
| `[ABORT Ti]` | Ti has been aborted |
| `[START CKPT (T1, T2, ...)]` | Checkpoint started; listed transactions are currently active |
| `[END CKPT]` | Checkpoint completed |

**Stable storage:** Assumed to survive any system crash (typically RAID + battery backup). Log writes are always to stable storage before data is modified.

---

## Update Strategies

### Immediate Update (Most Common in Practice)

- Changes are written to the database buffer (and potentially disk) **before** the transaction commits
- Both old and new values are logged → supports **both UNDO and REDO**

**Recovery requirements:**
- **UNDO:** Uncommitted transactions that have modified the database must be undone
- **REDO:** Committed transactions whose changes may not have reached disk must be redone

### Deferred Update (No-Steal / No-Undo)

- Changes are **not** written to disk until the transaction commits
- Modifications exist only in log and memory buffers until commit
- If transaction aborts, simply discard buffered changes — **no UNDO needed**
- Recovery: only **REDO** committed transactions

**Best suited for:** Long-lived transactions with few rollbacks (PYQ 23-24 Q13) — the overhead of buffering all updates in memory is acceptable when rollbacks are rare.

---

## WAL Protocol

**Write-Ahead Logging (WAL):** The log record for any modification **must be written to stable storage before the corresponding data page is written to disk**.

This guarantees:
1. On crash: log has old values to undo uncommitted changes
2. On crash: log has new values to redo committed changes not yet on disk

> WAL is the fundamental correctness requirement for log-based recovery.

---

## Checkpointing

**Purpose:** Limit the amount of log that needs to be replayed after a crash — avoids scanning from the beginning of the log.

**Two conditions to complete a checkpoint:**
1. Flush all log records currently in main memory (log buffer) to **stable storage**
2. Flush all modified (dirty) buffer blocks to **disk**

After the checkpoint, any transaction that committed before `END CKPT` is guaranteed to have its changes on disk — no REDO needed for those.

---

## Recovery Algorithm — Immediate Update with Checkpoints

### Step-by-Step Procedure

1. Scan the log **backwards** from the crash point to find the most recent `[START CKPT (T1, T2, ...)]`
2. Note the active transaction list from the CKPT record
3. Continue scanning backwards to find `[START Ti]` for each transaction active at checkpoint
4. **Classify each transaction:**

| Classification | Condition | Action |
|---------------|-----------|--------|
| **REDO** | `[COMMIT Ti]` found in log | Re-apply all new values (scan forward) |
| **UNDO** | `[START Ti]` found but NO `[COMMIT Ti]` | Remove partial changes (restore old values, scan backward) |

5. **Undo phase:** Scan log backwards; for each `[Ti, X, old, new]` where Ti ∈ undo-list → set X = old_val
6. **Redo phase:** Scan log forwards from earliest relevant start; for each `[Ti, X, old, new]` where Ti ∈ redo-list → set X = new_val

---

## Worked Example — Immediate Update Recovery (PYQ 22-23 Q5a)

**Log (top = earliest, bottom = last before crash):**
```
[START T1]
[START T2]
[T1, A, 10, 20]
[T2, B, 15, 25]
[T1, C, 30, 40]
[COMMIT T1]
[START T3]
[T2, A, 20, 30]
[T3, C, 40, 50]
[COMMIT T2]
[START T4]
[T4, A, 30, 35]
[START CKPT (T3, T4)]
[END CKPT]
[COMMIT T3]
─── CRASH ───
```

**Classification:**

| Transaction | Commit in Log? | Active at CKPT? | Result |
|-------------|---------------|-----------------|--------|
| T1 | Yes (before CKPT) | No | REDO |
| T2 | Yes (before CKPT) | No | REDO |
| T3 | Yes (after CKPT) | Yes | REDO |
| T4 | No | Yes | **UNDO** |

**Undo phase (scan backwards for T4):**
- `[T4, A, 30, 35]` → restore A = 30

**Redo phase (scan forwards from START T1):**
- T1: A: 10→20, C: 30→40
- T2: B: 15→25, A: 20→30
- T3: C: 40→50

**Final database state after recovery:**

| Item | Value |
|------|-------|
| A | 30 |
| B | 25 |
| C | 50 |

---

## Undoing a Committed Transaction (PYQ 23-24 Q4)

**Question:** How do you undo a transaction that has already committed?

**Answer:** Use a **compensating (compensatory) transaction** — a new transaction that performs the inverse operations.

You cannot simply "undo" a committed transaction because:
- Its changes are permanent (durability)
- Other transactions may have read and used its committed values

A compensating transaction applies the logical inverse (e.g., if T inserted row R, the compensating transaction deletes R). This creates a proper audit trail and maintains log integrity.

---

## Shadow Paging (PYQ 23-24 Short Q3)

### Mechanism
- Maintain two page tables: **current page table** (active) and **shadow page table** (last committed state)
- Writes use **copy-on-write**: never modify an existing page; write to a new disk location
- On commit: atomically replace the shadow page table pointer with the current page table
- On crash: discard the current page table; the shadow table is the consistent state

### Why Shadow Paging is NOT Suitable for General DBMS

| Problem | Explanation |
|---------|-------------|
| Poor concurrency support | Designed for single-user/single-transaction; multi-transaction coordination is complex |
| Fragmentation | After multiple commits, logically related pages scatter across disk; hurts sequential I/O |
| High overhead for large DBs | Copying and swapping huge page tables on every commit is expensive |
| No fine-grained undo | Cannot undo individual operations; only all-or-nothing rollback |

**Suitable for:** File systems (journaling), SQLite rollback journal, single-user embedded databases.

---

## Checkpoint Purpose (PYQ 23-24 Short Q4)

**Purpose:** Checkpointing **limits the amount of log that must be scanned** (redone) during recovery.

Without checkpoints, recovery would require replaying the log from the very beginning — impractical as databases grow.

**Two conditions that must be met to complete a checkpoint:**
1. All log records in the log buffer must be flushed to **stable storage**
2. All modified (dirty) buffer blocks must be flushed to **disk**

---

## Inconsistent Analysis — Worked Example (PYQ 17-18 Q2)

A transaction T2 computes a sum while T1 transfers 100 between accounts.

**Initial state:** X = 500, Z = 250

| Time | T1 | T2 |
|------|----|----|
| t1 | Begin | |
| t2 | Read(X) = 500 | Begin; Sum = 0 |
| t3 | X = 500 − 100 = 400 | Read(X) = 500; Sum += 500 → Sum = 500 |
| t4 | Write(X=400) | |
| t5 | Read(Z) = 250 | |
| t6 | Z = 250 + 100 = 350 | |
| t7 | Write(Z=350); Commit | Read(Z) = 350; Sum += 350 → Sum = 850 |
| t8 | | Write(Sum=850); Commit |

**Result:** T2 computes Sum = **850** instead of 750.

**Explanation:** T2 read X before T1's debit but Z after T1's credit — it observed a mix of old and new values. This is the **Inconsistent Analysis** problem (also called unrepeatable read or phantom read). Concurrency control (e.g., 2PL) prevents this by ensuring T2 either sees the old state or the new state, but not a mix.

---

## Common Exam Patterns

### Factual Questions
- What does WAL stand for and what does it require? (Write-Ahead Logging; log record written before data page written to disk)
- What is the purpose of checkpointing? (Limit log replay work during recovery; avoid scanning from log beginning)
- What are the two phases of immediate update recovery? (Undo phase for uncommitted transactions; redo phase for committed transactions)

### Conceptual Questions
- Why can't we simply undo a committed transaction? (Durability — other transactions may have built on its changes)
- Compare immediate update and deferred update — when is each preferred?
  (Immediate: standard, handles crashes at any point; Deferred: simpler recovery (no undo), but requires all updates in memory until commit — good for short transactions)
- Why is shadow paging not suitable for multi-user DBMS? (Poor concurrency support, fragmentation, high overhead for large databases)

### Solving Questions

**Full recovery walkthrough from log:**
1. Identify checkpoint and active transaction list
2. Find START records for all active transactions
3. Classify every transaction as REDO or UNDO
4. Execute undo phase (backwards scan)
5. Execute redo phase (forwards scan)
6. State final values of all modified items

**Check if a recovery scheme maintains WAL:**
- For each write to disk in the execution trace, verify that the corresponding log record was written first
