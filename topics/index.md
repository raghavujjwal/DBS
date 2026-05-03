---
layout: default
title: Topic-wise Study Guide
---

# Topic-wise Study Guide

Each topic is broken down by the **type of question** asked in exams. Use this to calibrate your preparation — some topics demand memorization, others require deep understanding, and many require practice with algorithms.

<div class="legend">
  <span class="badge badge-factual">Factual</span> Memorize
  <span class="badge badge-conceptual">Conceptual</span> Understand
  <span class="badge badge-solving">Solving</span> Practice
</div>

<div class="toc">

**Table of Contents**

- [1. ER Model & Data Modeling](#1-er-model--data-modeling)
- [2. Mapping ER to Relations](#2-mapping-er-to-relations)
- [3. Relational Model Fundamentals](#3-relational-model-fundamentals)
- [4. Relational Algebra](#4-relational-algebra)
- [5. SQL](#5-sql)
- [6. Functional Dependencies & Closure](#6-functional-dependencies--closure)
- [7. Normal Forms (1NF through BCNF)](#7-normal-forms-1nf-through-bcnf)
- [8. Decomposition Properties](#8-decomposition-properties)
- [9. Multivalued Dependencies & 4NF](#9-multivalued-dependencies--4nf)
- [10. Storage & File Organization](#10-storage--file-organization)
- [11. Indexing — B+ Trees](#11-indexing--b-trees)
- [12. Hashing (Static, Extendible, Linear)](#12-hashing-static-extendible-linear)
- [13. Multidimensional Indexing](#13-multidimensional-indexing)
- [14. Query Processing & Optimization](#14-query-processing--optimization)
- [15. Transaction Concepts & ACID](#15-transaction-concepts--acid)
- [16. Concurrency Control — Locking](#16-concurrency-control--locking)
- [17. Concurrency Control — Timestamps](#17-concurrency-control--timestamps)
- [18. Serializability](#18-serializability)
- [19. Recoverability & Cascadelessness](#19-recoverability--cascadelessness)
- [20. Crash Recovery](#20-crash-recovery)

</div>

---

## 1. ER Model & Data Modeling

**Exam Frequency:** <span class="badge badge-low">Low</span> — appears in MCQs occasionally, rarely as a full problem.

### <span class="badge badge-factual">Factual</span>

- What is an entity, attribute, relationship?
- Difference between strong and weak entity sets
- What is a partial key? What is a discriminator?
- Types of attributes: simple, composite, derived, multi-valued
- Participation constraints: total vs partial
- Cardinality ratios: 1:1, 1:N, M:N

### <span class="badge badge-conceptual">Conceptual</span>

- When to model something as an entity vs an attribute?
- When to use generalization/specialization?
- Why can't weak entities exist without their owner?
- Difference between conceptual, logical, and physical data models

### <span class="badge badge-solving">Solving</span>

- Draw an ER diagram from a textual description
- Identify entities, relationships, and constraints from a scenario

---

## 2. Mapping ER to Relations

**Exam Frequency:** <span class="badge badge-medium">Medium</span> — tested as MCQs (23-24) and embedded within normalization problems.

### <span class="badge badge-factual">Factual</span>

- Rules for mapping strong entities, weak entities, 1:1, 1:N, M:N relationships
- Where does the foreign key go in each cardinality ratio?
- How are multi-valued attributes mapped?

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (23-24 Q16, Q20):** Given a 1:1 relationship with optional participation, where should the foreign key be placed? Why?
- When should two entity types be merged into one relation?
- Impact of participation constraints on mapping choices (NULL foreign keys)

### <span class="badge badge-solving">Solving</span>

- Convert a given ER diagram to a relational schema
- Identify primary keys and foreign keys in the mapped schema

> **Exam tip:** 23-24 had 2 MCQs specifically about 1:1 optional mapping decisions. Know the trade-offs of merging vs. separate tables with foreign keys.

---

## 3. Relational Model Fundamentals

**Exam Frequency:** <span class="badge badge-medium">Medium</span> — appears as MCQs on integrity constraints and NULLs.

### <span class="badge badge-factual">Factual</span>

- Definitions: relation, tuple, attribute, domain, degree, cardinality
- Entity integrity constraint (PK cannot be NULL)
- Referential integrity constraint (FK must reference valid PK or be NULL)
- What is a superkey, candidate key, primary key?

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (23-24 Q15, Q17):** What does referential integrity guarantee? Which operations can violate it?
- **PYQ pattern (23-24 Short Q1):** Which operations on table A and table B violate a referential integrity constraint from A to B?
- 3-valued logic with NULLs — what does `NULL = NULL` return?
- Problems NULLs create with aggregates (`COUNT(col)` vs `COUNT(*)`)

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (23-24 Short Q5):** Given R(A,B,C), what is the maximum number of candidate keys R can have simultaneously? Extend to 4 attributes.
- Determine superkeys, candidate keys from a set of FDs

---

## 4. Relational Algebra

**Exam Frequency:** <span class="badge badge-high">High</span> — appears in both MCQ and open-book sections regularly.

### <span class="badge badge-factual">Factual</span>

- Six basic operators: σ (select), π (project), ∪ (union), − (set difference), × (Cartesian product), ρ (rename)
- Derived operators: ⋈ (join), ∩ (intersection), ÷ (division)
- Properties of each operator (commutative, associative)

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (23-24 Short Q8):** Express the division operator using basic RA operators
- Difference between natural join, theta join, equi-join, outer joins
- Why is RA "operational" while relational calculus is "declarative"?

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (22-23 Q3):** Convert a given SQL query to Relational Algebra
- **PYQ pattern (17-18 Part B Q1):** Write RA expressions for real-world queries (e.g., find students repeating courses)
- Construct RA expression trees

> **Exam tip:** Know the division operator's expansion — it's been asked directly. Practice converting between SQL and RA.

---

## 5. SQL

**Exam Frequency:** <span class="badge badge-high">High</span> — consistently tested, especially views, triggers, and T-SQL.

### <span class="badge badge-factual">Factual</span>

- DDL vs DML vs DCL commands
- Syntax for `CREATE VIEW`, `CREATE TRIGGER`
- What SQL standard features exist (23-24 Q8): access rights, not disk geometry
- Aggregate functions and GROUP BY behavior with NULLs

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (17-18 Part B Q1):** What type of database object should a temporary relation be if the exercise recurs every semester? (Materialized view)
- Difference between views and materialized views
- When do views update correctly?

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (17-18 Part C Q1-Q4):** Write SQL queries involving:
  - Views with date arithmetic (`DATEADD`, `GETDATE`)
  - Pattern matching (`LIKE 'S%T'`)
  - Triggers on insert that check capacity
  - T-SQL with conditions (reservation type = MAP, Hno = GH7X2)
- Write correlated subqueries, joins, nested queries

---

## 6. Functional Dependencies & Closure

**Exam Frequency:** <span class="badge badge-high">Very High</span> — appears in every single paper.

### <span class="badge badge-factual">Factual</span>

- Armstrong's axioms: reflexivity, augmentation, transitivity
- Derived rules: union, decomposition, pseudo-transitivity
- Definition of closure of an attribute set (X⁺)
- Definition of canonical/minimal cover

### <span class="badge badge-conceptual">Conceptual</span>

- How to determine if an FD is implied by a set F (check if RHS ⊆ X⁺)
- Why do we compute canonical cover? (Minimal set of FDs to enforce)

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (23-24 Q7):** Compute closure of {A} given F = {A→BC, B→D, AB→D}
- **PYQ pattern (22-23 Q2a, Q7a):** Compute closure of each individual attribute
- **PYQ pattern (21-22 Q9b):** Find the canonical/minimal cover of a relation
  - Steps: combine RHS, remove extraneous LHS attributes, remove redundant FDs
- Find all candidate keys from a set of FDs

> **Exam tip:** Closure computation appears in EVERY paper. Practice until it's mechanical.

---

## 7. Normal Forms (1NF through BCNF)

**Exam Frequency:** <span class="badge badge-high">Very High</span> — every paper, multiple questions.

### <span class="badge badge-factual">Factual</span>

- 1NF: atomic values only
- 2NF: no partial dependency of non-prime attributes on candidate keys
- 3NF: for every FD X→A, either X is a superkey or A is prime
- BCNF: for every non-trivial FD X→A, X is a superkey
- BCNF ⊂ 3NF ⊂ 2NF ⊂ 1NF

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (23-24 Q10):** "R is in 3NF" — which statement can be inferred?
- **PYQ pattern (23-24 Q18):** Given R(p,q,r,s,t) with specific FDs, identify the normal form
- **PYQ pattern (21-22 Q2):** BCNF vs 3NF — how to distinguish which decomposition is which?
- Why BCNF decomposition may not be dependency-preserving
- Why 3NF always allows dependency-preserving decomposition
- **PYQ pattern (23-24 Q19):** Data dictionary benefits — which is NOT a benefit?

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (22-23 Q7):** Full pipeline: compute closures → find candidate keys → identify highest normal form → justify
- Decompose a relation into BCNF using the BCNF decomposition algorithm
- Decompose a relation into 3NF using the synthesis algorithm

> **Exam tip:** You must be able to identify the exact normal form AND justify it. "What normal form is this?" appears in at least 2 questions per paper.

---

## 8. Decomposition Properties

**Exam Frequency:** <span class="badge badge-high">Very High</span> — tested alongside normalization in every paper.

### <span class="badge badge-factual">Factual</span>

- Lossless join: R1 ∩ R2 → R1 or R1 ∩ R2 → R2
- Dependency preservation: ∪ F_Ri = F⁺ (restrictions of F to decomposed relations cover F)
- Sufficient condition for lossless binary decomposition: common attributes form a key in at least one part

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (21-22 Q3):** Given A→B and C→D, is R1(AB), R2(CD) lossless? dependency-preserving?
- **PYQ pattern (21-22 Q2):** How to distinguish BCNF vs 3NF decomposition using these properties?
- **PYQ pattern (17-18 Q2):** State sufficient and necessary conditions for dependency preservation
- **PYQ pattern (23-24 Short Q7):** Give a decomposition that is dependency-preserving but NOT lossless

### <span class="badge badge-solving">Solving</span>

- Test a given decomposition for lossless join (chase algorithm or attribute closure method)
- Test for dependency preservation (compute restrictions and check coverage)
- **PYQ pattern (23-24 Short Q6):** Construct example tuples that demonstrate an MVD does NOT hold

---

## 9. Multivalued Dependencies & 4NF

**Exam Frequency:** <span class="badge badge-medium">Medium</span> — growing trend, appeared in 22-23 and 23-24.

### <span class="badge badge-factual">Factual</span>

- MVD: X →→ Y means for a given X, the set of Y values is independent of R − X − Y values
- 4NF: for every MVD X →→ Y, X is a superkey (or the MVD is trivial)
- Every FD is also an MVD
- **PYQ pattern (23-24 Q3):** Highest normal form based on MVDs is 4NF

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (23-24 Short Q2):** Fill in: "For a given ___, the values of ___ and ___ are ___ of each other"
- Why BCNF doesn't handle redundancy from MVDs
- When does a relation in BCNF still have anomalies?

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (22-23 Q2, Q8):** Decompose a relation into 4NF, identify candidate keys and foreign keys
- **PYQ pattern (23-24 Short Q6):** Provide example tuples where a specific MVD does not hold

> **Exam tip:** 4NF decomposition questions ask you to list decomposed relations with their candidate keys AND foreign keys. Don't forget the FKs.

---

## 10. Storage & File Organization

**Exam Frequency:** <span class="badge badge-high">High</span> — numerical problems appear regularly.

### <span class="badge badge-factual">Factual</span>

- Storage hierarchy: cache → main memory → secondary (disk) → tertiary (tape)
- Access time at each level
- Fixed-length vs variable-length records
- Spanned vs unspanned record organization
- Heap file, sorted file, hash file — when to use each
- **PYQ pattern (23-24 Short Q10):** Two ways to handle variable-length records (slotted pages, pointer-based)

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (21-22 Q6-Q8):** Which file organization is best for: range search? insert-heavy? exact match?
  - Range → Sorted, Insert → Heap, Exact match → Hash
- Trade-offs between file organizations

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (21-22 Q8):** Full calculation pipeline:
  - Record size = sum of field sizes
  - Blocking factor = ⌊B / R⌋
  - Number of blocks = ⌈n / bfr⌉
  - Binary search accesses = ⌈log₂(blocks)⌉
- **PYQ pattern (17-18 Q1):** Partitioned hash table — compute bucket number for a record, average buckets searched
- Primary index calculations (index blocking factor, multi-level index levels)

> **Exam tip:** These are "free marks" if you know the formulas. Practice the full chain: record size → blocking factor → blocks → index entries → levels → accesses.

---

## 11. Indexing — B+ Trees

**Exam Frequency:** <span class="badge badge-high">Very High</span> — appears in every paper with both MCQs and long problems.

### <span class="badge badge-factual">Factual</span>

- B+ tree properties: balanced, leaf nodes linked, only leaves have data pointers
- Non-leaf nodes: ⌈n/2⌉ to n children
- Leaf nodes: ⌈(n-1)/2⌉ to n-1 search keys
- **PYQ pattern (21-22 Q4):** Non-leaf nodes do NOT have pointers to data records
- Degree calculation: n = ⌊(B − P) / (K + P)⌋ + 1

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (23-24 Q1):** Which index structure is NEVER needed for clustering? (Hash — clustering requires order)
- **PYQ pattern (23-24 Q2):** Which index is suitable for low-cardinality attributes? (Bitmap)
- **PYQ pattern (23-24 Q11):** What characterizes POOR B+ tree performance? (Long branches)
- **PYQ pattern (23-24 Q12):** Are frequently-modified columns good for indexing? (No)
- **PYQ pattern (21-22 Q6):** Performance and storage utilization problems of bulk-loading B+ tree by sequential inserts

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (21-22 Q1):** Calculate optimal degree of B+ tree given key size, pointer size, block size
- **PYQ pattern (21-22 Q4):** Construct B+ tree from sequential inserts, show all intermediate states
- **PYQ pattern (22-23 Q6):** Calculate: blocks for table → binary search accesses on primary index → max B+ tree height → accesses via B+ tree
- **PYQ pattern (22-23 Q6b):** Insert into existing B+ tree, show splits and final state
- Calculate maximum height of B+ tree given n and number of records

> **Exam tip:** B+ tree construction with intermediate states is the most common long-answer question. Practice insertions that cause splits at multiple levels.

---

## 12. Hashing (Static, Extendible, Linear)

**Exam Frequency:** <span class="badge badge-medium">Medium</span> — full problems in 17-18 and 21-22.

### <span class="badge badge-factual">Factual</span>

- Static hashing: h(K) mod M gives bucket number
- Extendible hashing: global depth, local depth, directory doubling
- Linear hashing: split pointer, level, overflow chains
- Bucket splitting rules

### <span class="badge badge-conceptual">Conceptual</span>

- When does the directory double in extendible hashing?
- What triggers a split in linear hashing?
- Advantages of dynamic hashing over static

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (21-22 Q7):** Given a sequence of records and bucket capacity of 4:
  - Show final state of extendible hashing (directory, local depth, global depth)
  - Show final state of linear hashing (lower-order bits)
- **PYQ pattern (17-18 Q1):** Partitioned hash — compute bucket from multi-attribute hash function

> **Exam tip:** You must show the step-by-step state of the hash structure. Don't just show the final state.

---

## 13. Multidimensional Indexing

**Exam Frequency:** <span class="badge badge-low">Low</span> — appeared in 17-18, concepts from lectures.

### <span class="badge badge-factual">Factual</span>

- Grid file: uniform partitions along each dimension
- R-tree: MBRs (Minimum Bounding Rectangles), balanced tree
- Types of queries: partial match, range, nearest neighbor, where-am-I

### <span class="badge badge-conceptual">Conceptual</span>

- When to use grid files vs R-trees vs kd-trees
- How does MINDIST pruning work in R-tree NN search?

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (17-18 Q4):** Grid file nearest-neighbor query — given a point and closest candidate, determine which other buckets must be searched
- MINDIST computation for R-tree NN queries
- Calculate I/Os for grid file queries

---

## 14. Query Processing & Optimization

**Exam Frequency:** <span class="badge badge-medium">Medium</span> — short answers and heuristic questions.

### <span class="badge badge-factual">Factual</span>

- Steps: parsing → optimization → evaluation
- Cost measure: number of disk block transfers
- Join algorithms: nested loop, block nested loop, indexed nested loop, sort-merge, hash join
- Selection algorithms: linear scan, binary search (on sorted), index-based

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (21-22 Q9a):** List 5 ways to improve SQL query performance with examples
- Heuristics: push selections down, push projections down, avoid Cartesian products
- **PYQ pattern (17-18 Q3):** Given 6 relations with no info, give a heuristic to avoid enumerating all join orders. How many join orders exist?

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (17-18 Q5):** Graphically depict hash join between two relations with annotated steps
- Calculate cost of different join strategies given block counts and memory
- Construct RA expression trees based on heuristic optimization

---

## 15. Transaction Concepts & ACID

**Exam Frequency:** <span class="badge badge-high">High</span> — MCQs on states and properties, plus full problems.

### <span class="badge badge-factual">Factual</span>

- ACID: Atomicity, Consistency, Isolation, Durability
- Transaction states: active → partially committed → committed / failed → aborted
- **PYQ pattern (23-24 Q5):** A transaction has terminated if it has committed or failed
- **PYQ pattern (23-24 Q6):** A successfully completed transaction → committed state
- **PYQ pattern (23-24 Q4):** Undo effects of committed transaction → compensatory transaction
- **PYQ pattern (23-24 Short Q9):** What is commit dependency?

### <span class="badge badge-conceptual">Conceptual</span>

- Which ACID property does concurrency control ensure? (Isolation)
- Which ACID property does crash recovery ensure? (Atomicity, Durability)
- Why do we need transaction management?

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (17-18 Q2):** Trace through an interleaved schedule, compute variable values at each step, identify if expected results are produced

---

## 16. Concurrency Control — Locking

**Exam Frequency:** <span class="badge badge-high">Very High</span> — detailed protocol application in every paper.

### <span class="badge badge-factual">Factual</span>

- Lock types: shared (S), exclusive (X)
- Compatibility matrix: S-S compatible, all others incompatible
- 2PL variants: simple, strict, rigorous, conservative
- Simple 2PL: growing phase + shrinking phase
- Strict 2PL: hold exclusive locks until commit
- Rigorous 2PL: hold ALL locks until commit
- **PYQ pattern (21-22 Q9):** Two-Phase Locking requires key pairs → FALSE

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (23-24 Q9):** What problem do locks introduce? (Performance degradation)
- **PYQ pattern (22-23 Q1):** Compare timestamping and 2PL across 5 dimensions:
  - Conflict serializability, deadlock freedom, view serializability, recoverability, cascading rollback prevention
- 2PL guarantees conflict serializability but NOT deadlock freedom
- Strict 2PL also prevents cascading rollback
- Rigorous 2PL also ensures recoverability

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (22-23 Q3):** Apply simple 2PL to a schedule — list all lock/unlock operations, identify deadlocks, show diagrammatically
- **PYQ pattern (22-23 Q3b):** Apply strict 2PL and conservative 2PL to the same schedule
- **PYQ pattern (21-22 Q2):** Apply rigorous 2PL to a schedule

> **Exam tip:** You need to show the lock table step-by-step. Mark the growing phase and shrinking phase clearly. Identify deadlocks by drawing wait-for graphs.

---

## 17. Concurrency Control — Timestamps

**Exam Frequency:** <span class="badge badge-high">High</span> — detailed protocol application.

### <span class="badge badge-factual">Factual</span>

- Each transaction gets a timestamp at start
- Read rule: if TS(T) < W-TS(X), reject (read too late)
- Write rule: if TS(T) < R-TS(X), reject (write too late); if TS(T) < W-TS(X), Thomas write rule may apply
- Timestamp protocol ensures conflict serializability and deadlock freedom

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (21-22 Q5b):** Does timestamp ordering ensure: conflict serializability? view serializability? deadlock freedom? cascadelessness? recoverability?
- Timestamp vs 2PL trade-offs

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (22-23 Q4):** Apply basic timestamp protocol to a schedule — provide R-TS and W-TS for each data item at each step, identify conflicts and how they're resolved
- **PYQ pattern (21-22 Q5a):** Rewrite a schedule after implementing timestamp ordering

---

## 18. Serializability

**Exam Frequency:** <span class="badge badge-high">Very High</span> — tested in every paper.

### <span class="badge badge-factual">Factual</span>

- Conflict equivalent: same operations, same conflict order
- Conflict serializable: conflict equivalent to some serial schedule
- View equivalent: same reads-from relationships, same final writes
- View serializable: view equivalent to some serial schedule
- Every conflict-serializable schedule is also view-serializable (not vice versa)

### <span class="badge badge-conceptual">Conceptual</span>

- Why is testing view serializability NP-complete?
- What makes two operations conflict? (Same data item, at least one write, different transactions)

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (21-22 Q1, 22-23 Q1):** For multiple schedules:
  1. Draw precedence graph
  2. Check for cycles → conflict serializable if acyclic
  3. If conflict serializable, give equivalent serial order (topological sort)
  4. If not, check view serializability
- Practice: Given S: R1(A), R2(A), W1(A)... draw all conflict edges between transactions

> **Exam tip:** This is the most common open-book question. You MUST know how to draw precedence graphs flawlessly. Practice with 3-4 transaction schedules.

---

## 19. Recoverability & Cascadelessness

**Exam Frequency:** <span class="badge badge-high">High</span> — tested alongside serializability.

### <span class="badge badge-factual">Factual</span>

- Recoverable: if Ti reads from Tj, then Tj commits before Ti
- Cascadeless (avoids cascading rollback): Ti reads X only after Tj that wrote X has committed
- Strict: Ti neither reads nor writes X until Tj that wrote X has committed/aborted
- Strict ⊂ Cascadeless ⊂ Recoverable

### <span class="badge badge-conceptual">Conceptual</span>

- Why are non-recoverable schedules dangerous? (Committed transaction depends on rolled-back one)
- Why do we want cascadelessness? (Avoid chain of rollbacks)

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (22-23 Q2a-c):** For given schedules, determine:
  - Is it recoverable? (Check commit order respects read-from)
  - Is it cascadeless? (Check that reads happen only after writer commits)
  - Justify each answer with specific transaction pairs
- **PYQ pattern (21-22 Q1b):** Check recoverability and cascadelessness of multiple schedules

---

## 20. Crash Recovery

**Exam Frequency:** <span class="badge badge-high">High</span> — log analysis problems in 3 of 4 papers.

### <span class="badge badge-factual">Factual</span>

- Log record format: [Ti, X, old_value, new_value]
- Immediate update: changes written to DB before commit
- Deferred update: changes written only after commit
- **PYQ pattern (23-24 Q13):** Deferred update is best for long-lived transactions with few rollbacks
- Checkpoint: [START CKPT (active transactions)], [END CKPT]
- **PYQ pattern (23-24 Short Q4):** Purpose and two conditions for checkpoint operation
- WAL (Write-Ahead Logging): log must be written before data page
- **PYQ pattern (23-24 Short Q3):** Shadow paging is not suitable for databases — why? (Doesn't handle concurrent transactions well; suitable for file systems)

### <span class="badge badge-conceptual">Conceptual</span>

- **PYQ pattern (23-24 Q10):** What is the main advantage of immediate update? (Changes stored to disk before commit)
- Undo vs Redo: when is each needed?
- What transactions go into the undo list vs redo list after a crash?
- Role of checkpoints in reducing recovery work

### <span class="badge badge-solving">Solving</span>

- **PYQ pattern (22-23 Q5a):** Given a log with checkpoint and crash point:
  1. Identify active transactions at crash
  2. Determine undo set and redo set
  3. Apply undo operations (reverse order)
  4. Apply redo operations (forward order)
  5. State final values of all data items
- **PYQ pattern (22-23 Q5b):** WAL recovery — if no changes written to disk, recover using log
- **PYQ pattern (21-22 Q3):** Step-by-step recovery with immediate update and checkpointing — which transactions rolled back, which redone, any cascading rollback?

> **Exam tip:** Practice identifying which transactions are in-progress at crash time vs already committed. The undo/redo classification is the crux of these problems.

---

## Cross-Topic Patterns

Some exam questions combine multiple topics. Watch for:

1. **Normalization + Decomposition + MVD pipeline:** Compute closures → find candidate keys → determine normal form → decompose → verify lossless + dependency-preserving (22-23 Q7, Q8)
2. **Serializability + Recoverability + Locking:** Test a schedule for all three properties, then apply 2PL (22-23 Q1-Q3)
3. **Indexing + File Organization:** Calculate storage parameters, then build index on top (21-22 Q8)
4. **SQL + RA conversion:** Write SQL, convert to RA, or vice versa (22-23 Q3, 17-18 Part C)
