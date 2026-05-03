---
layout: default
title: PYQ Breakdown
---

# Previous Year Question Breakdown

Every question from 4 comprehensive exams, tagged by topic, type, and difficulty.

<div class="legend">
  <span class="badge badge-factual">F</span> Factual
  <span class="badge badge-conceptual">C</span> Conceptual
  <span class="badge badge-solving">S</span> Solving
</div>

---

## 2023-24 Comprehensive (40 Marks, 60 min, Closed Book)

### Part I — MCQ (1 × 20 = 20 marks, 50% negative)

| # | Topic | Type | Question Summary |
|---|-------|------|-----------------|
| 1 | Indexing | <span class="badge badge-conceptual">C</span> | Which index is NEVER needed as clustering index? |
| 2 | Indexing | <span class="badge badge-conceptual">C</span> | Which index suits low-cardinality attributes? (Bitmap) |
| 3 | Normalization | <span class="badge badge-factual">F</span> | Highest NF based on MVD? (4NF) |
| 4 | Crash Recovery | <span class="badge badge-factual">F</span> | Operation to undo committed transaction effects? (Compensatory) |
| 5 | Transactions | <span class="badge badge-factual">F</span> | Transaction terminates when? (Committed or Failed) |
| 6 | Transactions | <span class="badge badge-factual">F</span> | Successfully completed transaction state? (Committed) |
| 7 | FDs & Closure | <span class="badge badge-solving">S</span> | Closure of {A} given F = {A→BC, B→D, AB→D} |
| 8 | SQL | <span class="badge badge-factual">F</span> | SQL features — which function does SQL support? |
| 9 | Concurrency | <span class="badge badge-conceptual">C</span> | Problem from introducing locks? (Performance degradation) |
| 10 | Normalization | <span class="badge badge-conceptual">C</span> | Relation in 3NF — what can be inferred? |
| 11 | B+ Tree | <span class="badge badge-conceptual">C</span> | Which property means POOR B+ tree performance? (Long branches) |
| 12 | Indexing | <span class="badge badge-conceptual">C</span> | True statements about indexes? |
| 13 | Crash Recovery | <span class="badge badge-conceptual">C</span> | Best algorithm for long-lived transactions with few rollbacks? |
| 14 | Concurrency | <span class="badge badge-solving">S</span> | Identify consistency problem from schedule (Lost Update) |
| 15 | Relational Model | <span class="badge badge-factual">F</span> | Referential integrity definition |
| 16 | ER Mapping | <span class="badge badge-conceptual">C</span> | 1:1 optional mapping — where to put FK? |
| 17 | Relational Model | <span class="badge badge-conceptual">C</span> | What does referential integrity enforcement ensure? |
| 18 | Normalization | <span class="badge badge-solving">S</span> | Identify normal form of R(p,q,r,s,t) with given FDs |
| 19 | DBMS General | <span class="badge badge-factual">F</span> | Data dictionary benefit that is NOT valid? (Performance measurement) |
| 20 | ER Mapping | <span class="badge badge-conceptual">C</span> | 1:1 optional at both ends — mapping strategy? |

### Part II — Short Answers (2 × 10 = 20 marks)

| # | Topic | Type | Question Summary |
|---|-------|------|-----------------|
| 1 | Relational Model | <span class="badge badge-conceptual">C</span> | Which operations on A/B violate referential integrity constraint from A to B? |
| 2 | MVD / 4NF | <span class="badge badge-conceptual">C</span> | Fill-in-the-blank: MVD A→→BC semantics |
| 3 | Crash Recovery | <span class="badge badge-conceptual">C</span> | Why is shadow paging unsuitable for DBMS? Where is it suitable? |
| 4 | Crash Recovery | <span class="badge badge-factual">F</span> | Purpose and conditions of checkpoint operation |
| 5 | Relational Model | <span class="badge badge-solving">S</span> | Max simultaneous candidate keys for R(A,B,C)? Extend to 4 attributes |
| 6 | MVD / 4NF | <span class="badge badge-solving">S</span> | Provide tuple data where FDs hold but MVD does not |
| 7 | Decomposition | <span class="badge badge-solving">S</span> | Example of dependency-preserving but not lossless decomposition |
| 8 | Relational Algebra | <span class="badge badge-solving">S</span> | Express division operator using basic RA operators |
| 9 | Transactions | <span class="badge badge-factual">F</span> | Define commit dependency and its significance |
| 10 | File Organization | <span class="badge badge-factual">F</span> | Two ways to handle variable-length records, show structure |

**Type Distribution:** Factual: 9, Conceptual: 11, Solving: 10

---

## 2022-23 Comprehensive (105 Marks)

### Part A — Closed Book (20 marks, 30 min)

| # | Topic | Type | Marks | Question Summary |
|---|-------|------|-------|-----------------|
| 1 | Concurrency | <span class="badge badge-conceptual">C</span> | 6 | Compare timestamp vs 2PL across 5 properties (fill table) |
| 2a | FDs & Closure | <span class="badge badge-solving">S</span> | 2 | Compute closure of each attribute (EID, PID, Role, Skill) |
| 2b | Normalization | <span class="badge badge-solving">S</span> | 2 | Find all candidate keys |
| 2c | 4NF | <span class="badge badge-solving">S</span> | 4 | Decompose into 4NF with PKs and FKs |
| 3 | Relational Algebra | <span class="badge badge-solving">S</span> | 2 | Convert SQL JOIN query to RA |
| 4 | DBMS General | <span class="badge badge-conceptual">C</span> | 4 | Why do modern apps prefer NoSQL? (Select all true) |

### Part B — Open Book (85 marks)

| # | Topic | Type | Marks | Question Summary |
|---|-------|------|-------|-----------------|
| 1a | Serializability | <span class="badge badge-solving">S</span> | 5 | Precedence graph, check conflict serializability of 4-transaction schedule |
| 1b | Serializability | <span class="badge badge-solving">S</span> | 5 | Check view serializability of 3-transaction schedule |
| 2a | Recoverability | <span class="badge badge-solving">S</span> | 5 | Determine if schedule is recoverable |
| 2b | Recoverability | <span class="badge badge-solving">S</span> | 5 | Determine recoverability AND cascadelessness |
| 2c | Recoverability | <span class="badge badge-solving">S</span> | 5 | Check two schedules for recoverability + cascadelessness |
| 3a | 2PL | <span class="badge badge-solving">S</span> | 5 | Apply simple 2PL, show lock/unlock, identify deadlocks |
| 3b | 2PL | <span class="badge badge-solving">S</span> | 5 | Apply strict and conservative 2PL |
| 4 | Timestamps | <span class="badge badge-solving">S</span> | 5 | Apply basic timestamp protocol with R-TS, W-TS |
| 5a | Crash Recovery | <span class="badge badge-solving">S</span> | 5 | Log-based recovery with checkpoint — undo/redo |
| 5b | Crash Recovery | <span class="badge badge-solving">S</span> | 5 | WAL recovery — recover from log |
| 6a | B+ Tree + Storage | <span class="badge badge-solving">S</span> | 5 | Calculate blocks, binary search accesses, B+ tree height |
| 6b | B+ Tree | <span class="badge badge-solving">S</span> | 5 | Construct B+ tree with sequential inserts, show all states |
| 7a | FDs & Closure | <span class="badge badge-solving">S</span> | 5 | Compute closure of each attribute |
| 7b | Normalization | <span class="badge badge-solving">S</span> | 5 | Find all candidate keys |
| 7c | Normalization | <span class="badge badge-conceptual">C</span> | 5 | Identify highest normal form with justification |
| 8a | 4NF | <span class="badge badge-solving">S</span> | 5 | Decompose into 4NF |
| 8b | 4NF | <span class="badge badge-solving">S</span> | 5 | Identify candidate keys and foreign keys for decomposed tables |

**Type Distribution:** Factual: 0, Conceptual: 3, Solving: 19

---

## 2021-22 Comprehensive (120 Marks)

### Part A — Closed Book MCQ (15 marks, 30 min)

| # | Topic | Type | Question Summary |
|---|-------|------|-----------------|
| 1 | B+ Tree | <span class="badge badge-solving">S</span> | Calculate optimal B+ tree degree (key=8B, block=512B, ptr=4B) |
| 2 | Normalization | <span class="badge badge-conceptual">C</span> | How to distinguish BCNF vs 3NF decomposition? |
| 3 | Decomposition | <span class="badge badge-solving">S</span> | A→B, C→D: is R1(AB), R2(CD) lossless/dep-preserving? |
| 4 | B+ Tree | <span class="badge badge-factual">F</span> | Which B+ tree statement is NOT correct? (Non-leaf pointers to data) |
| 5 | Normalization | <span class="badge badge-solving">S</span> | BCNF decomposition — check both sub-schemas, lossless + dep-preserving |
| 6 | File Organization | <span class="badge badge-conceptual">C</span> | Best file org for range search? (Sorted) |
| 7 | File Organization | <span class="badge badge-conceptual">C</span> | Best for inserts + unordered scans? (Heap) |
| 8 | File Organization | <span class="badge badge-conceptual">C</span> | Best for exact match search? (Hash) |
| 9 | 2PL | <span class="badge badge-factual">F</span> | Which 2PL statement is FALSE? |
| 10 | Crash Recovery | <span class="badge badge-conceptual">C</span> | Advantage of immediate update? |

### Part B — Open Book (105 marks)

| # | Topic | Type | Marks | Question Summary |
|---|-------|------|-------|-----------------|
| 1a | Serializability | <span class="badge badge-solving">S</span> | 12 | 3 schedules — conflict + view serializability, serial order |
| 1b | Recoverability | <span class="badge badge-solving">S</span> | 3 | Check recoverable + cascadeless for 3 schedules |
| 2a | 2PL | <span class="badge badge-solving">S</span> | 5 | Apply rigorous 2PL to schedule |
| 2b | 2PL | <span class="badge badge-conceptual">C</span> | 5 | Does rigorous 2PL ensure conflict/view serial, deadlock freedom, etc.? |
| 3 | Crash Recovery | <span class="badge badge-solving">S</span> | 10 | Step-by-step recovery with immediate update + checkpoint |
| 4 | B+ Tree | <span class="badge badge-solving">S</span> | 10 | Construct B+ tree for A-T (20 letters), n=4, show best full tree |
| 5a | Timestamps | <span class="badge badge-solving">S</span> | 5 | Implement timestamp ordering on a schedule |
| 5b | Timestamps | <span class="badge badge-conceptual">C</span> | 5 | What does timestamp protocol ensure? |
| 6a | B+ Tree | <span class="badge badge-conceptual">C</span> | 5 | Problems of building dense clustered B+ tree by sequential inserts |
| 6b | B+ Tree | <span class="badge badge-conceptual">C</span> | 5 | Modification to insertion routine to fix the problem |
| 7 | Hashing | <span class="badge badge-solving">S</span> | 10 | Extendible + linear hashing with 17 records |
| 8 | Storage + Indexing | <span class="badge badge-solving">S</span> | 20 | Full chain: blocking factor → blocks → primary index → multi-level |
| 9a | Query Optimization | <span class="badge badge-conceptual">C</span> | 5 | 5 ways to improve SQL query performance |
| 9b | FDs | <span class="badge badge-solving">S</span> | 5 | Find canonical/minimal cover |

**Type Distribution:** Factual: 2, Conceptual: 10, Solving: 14

---

## 2017-18 Comprehensive (80 Marks)

### Part A — Closed Book Short Answers (20 marks, 45 min)

| # | Topic | Type | Marks | Question Summary |
|---|-------|------|-------|-----------------|
| 1 | Hashing | <span class="badge badge-solving">S</span> | 4 | Partitioned hash: bucket number for record, avg buckets searched |
| 2 | Decomposition | <span class="badge badge-factual">F</span> | 4 | State sufficient and necessary conditions for dependency preservation |
| 3 | Query Optimization | <span class="badge badge-solving">S</span> | 4 | Heuristic for 6-relation join, RA expression tree, number of join orders |
| 4 | Materialized Views | <span class="badge badge-solving">S</span> | 4 | Incremental maintenance of full outer join view on inserts/deletes |
| 5 | Query Processing | <span class="badge badge-solving">S</span> | 4 | Graphically depict hash join (R=10 blocks, S=5 blocks, mod 5) |

### Part B — Open Book (30 marks)

| # | Topic | Type | Marks | Question Summary |
|---|-------|------|-------|-----------------|
| 1 | Relational Algebra | <span class="badge badge-solving">S</span> | 10 | RA expression for finding repeating students, what DB object to use |
| 2 | Transactions | <span class="badge badge-solving">S</span> | 10 | Trace interleaved schedule, populate value table, check correctness |
| 3 | Hashing | <span class="badge badge-solving">S</span> | 10 | Extendible hashing: max/min buckets after 8 inserts into full 4-bucket structure |
| 4 | MD Indexing | <span class="badge badge-solving">S</span> | 10 | Grid file NN query — determine additional buckets to search |

### Part C — SQL (20 marks)

| # | Topic | Type | Marks | Question Summary |
|---|-------|------|-------|-----------------|
| 1 | SQL | <span class="badge badge-solving">S</span> | 5 | Create view for guests arriving in 3 days |
| 2 | SQL | <span class="badge badge-solving">S</span> | 5 | View with pattern matching + aggregate query on view |
| 3 | SQL | <span class="badge badge-solving">S</span> | 5 | Write trigger for fully booked hotel + verification query |
| 4 | SQL | <span class="badge badge-solving">S</span> | 5 | T-SQL query with specific conditions |

**Type Distribution:** Factual: 1, Conceptual: 0, Solving: 12

---

## Aggregate Analysis Across All Papers

### Questions by Topic (all 4 papers combined)

| Topic | Total Questions | Factual | Conceptual | Solving |
|-------|:--------------:|:-------:|:----------:|:-------:|
| Normalization & FDs | 18 | 2 | 5 | 11 |
| Concurrency (2PL, TS) | 14 | 2 | 4 | 8 |
| Serializability & Recoverability | 12 | 0 | 1 | 11 |
| B+ Tree & Indexing | 12 | 2 | 5 | 5 |
| Crash Recovery | 8 | 2 | 3 | 3 |
| File Org & Storage | 6 | 1 | 3 | 2 |
| SQL & RA | 10 | 1 | 1 | 8 |
| Hashing | 4 | 0 | 0 | 4 |
| ER & Relational Model | 8 | 3 | 4 | 1 |
| Query Processing | 4 | 0 | 1 | 3 |
| MD Indexing & Others | 3 | 0 | 0 | 3 |

### Key Takeaway

> **~45% of all marks** come from solving-type questions on just 4 topics: normalization, serializability/recoverability, concurrency control protocols, and B+ tree operations. Master these with pen-and-paper practice.
