---
layout: default
title: Query Processing & Optimization
---

# Query Processing & Optimization

---

## Query Processing Pipeline

A SQL query goes through several transformation steps before execution:

```
SQL Query
    ↓  1. Parser & Translator
Relational Algebra Expression (query tree)
    ↓  2. Optimizer
Execution Plan (with chosen algorithms)
    ↓  3. Evaluator
Query Result
```

### Step 1: Parsing and Translation
- Check syntax validity
- Verify table/column names against the catalog
- Translate SQL to an initial relational algebra expression (usually a query tree)

### Step 2: Query Optimization
- Enumerate alternative plans (different orderings of joins, different algorithms)
- Estimate cost of each plan using statistics from the catalog
- Choose the plan with lowest estimated cost

### Step 3: Evaluation
- Execute the chosen plan
- Stream results using iterators (open/get_next/close interface) for pipelining

---

## Measures of Query Cost

**Primary cost metric:** Number of **block transfers** (disk I/Os).
In some models, seek time is also counted separately.

Notation used throughout:
- `b_r` = number of disk blocks in relation r
- `n_r` = number of tuples (records) in relation r
- `M` = number of memory buffer blocks available

---

## Selection Algorithms

| Algorithm | Cost (block transfers) | When to Use |
|-----------|----------------------|-------------|
| Linear scan | b_r | No index; works for any condition |
| Binary search | ⌈log₂(b_r)⌉ + k | Sorted file, equality/range on sort key |
| Primary index (equality) | ⌈log₂(b_i)⌉ + 1 | Primary B+ tree, key attribute |
| Primary index (range) | ⌈log₂(b_i)⌉ + n_blocks | Primary B+ tree, range on sort key |
| Secondary index (equality, key) | ⌈log₂(b_i)⌉ + 1 | Secondary B+ tree, key attribute |
| Secondary index (equality, non-key) | ⌈log₂(b_i)⌉ + n_r | Secondary B+ tree, duplicate keys (worst case: one I/O per record) |

**Conjunctive selection:** Condition is `c1 AND c2 AND ...`
- Use index for the most selective condition; filter remaining in memory
- If multiple indexes available: use each, then intersect record IDs

**Disjunctive selection:** Condition is `c1 OR c2 OR ...`
- Can use multiple indexes + union if ALL conditions have indexes
- Otherwise: linear scan is unavoidable

---

## External Merge Sort

Used when the relation to be sorted is too large to fit in memory.

### Phase 1: Create Sorted Runs
- Load M blocks at a time into memory
- Sort them in memory → write a sorted run to disk
- Total runs created: ⌈b_r / M⌉

### Phase 2: Merge Runs
- Merge up to M-1 runs at a time (need 1 buffer for output)
- Each merge pass reduces the number of runs by factor (M-1)
- Number of merge passes: ⌈log_{M-1}(⌈b_r/M⌉)⌉

### Total Cost
```
Cost = 2 × b_r × (1 + number of merge passes)
     = 2b_r × (1 + ⌈log_{M-1}(⌈b_r/M⌉)⌉)
```
(Each block is read and written once per pass; first factor 2 for initial run creation.)

---

## Join Algorithms

### 1. Nested Loop Join (NLJ)
```
for each tuple t_r in r:           ← outer relation
  for each tuple t_s in s:         ← inner relation
    if t_r ⋈ t_s: output
```

**Cost:**
```
n_r × b_s + b_r
```
- For each tuple of r, we scan all of s
- Choose the **smaller relation as outer** to minimize cost

**When to use:** When one relation is tiny enough to keep in memory; no index available.

---

### 2. Block Nested Loop Join (BNLJ)
```
for each block B_r of r:
  for each block B_s of s:
    for each tuple t_r in B_r:
      for each tuple t_s in B_s:
        if t_r ⋈ t_s: output
```

**Cost:**
```
⌈b_r / (M-2)⌉ × b_s + b_r
```
- Use M-2 blocks as input buffer for r (1 for s, 1 for output)
- M = available memory blocks

**Optimization:** If M is large enough to hold all of r → one pass over s.

---

### 3. Indexed Nested Loop Join
```
for each tuple t_r in r:
  use index on s to find tuples matching t_r's join attribute
```

**Cost:**
```
b_r + n_r × cost_per_index_lookup
```
- `cost_per_index_lookup` = B+ tree height + 1 for primary index; may be higher for secondary
- **Requires an index on the inner relation's join attribute**
- Best when r is small and s has a good index

---

### 4. Sort-Merge Join
```
1. Sort r on join attribute
2. Sort s on join attribute
3. Merge the two sorted files (like merge phase of merge-sort)
```

**Cost:**
```
cost_of_sorting_r + cost_of_sorting_s + b_r + b_s
  ≈ 2b_r(1 + log_{M-1}(b_r/M)) + 2b_s(1 + log_{M-1}(b_s/M)) + b_r + b_s
```

**When to use:**
- Both relations are already sorted (sort cost = 0)
- Result needs to be sorted anyway
- Large equi-join on a non-indexed attribute

---

### 5. Hash Join
```
Build phase:  hash r on join attribute → r0, r1, ..., r_{n-1}
Probe phase:  for each partition r_i:
                load r_i into memory
                hash s into the same partition s_i
                probe: for each s tuple, find matches in r_i
```

**Cost:**
```
3(b_r + b_s)     assuming b_r / M < M-2  (fits in memory)
```
- Factor 3: read both, write partitions (2×), then read and join (1×)

**Condition:** Number of partitions × (avg partition size) ≤ M. If r doesn't fit: use recursive partitioning (extra passes).

**When to use:** Large equi-joins without existing sorted order; typically fastest for large relations.

---

### Join Algorithm Summary

| Algorithm | Cost | Best When |
|-----------|------|-----------|
| Nested Loop | n_r × b_s + b_r | Outer is tiny |
| Block Nested Loop | ⌈b_r/(M-2)⌉ × b_s + b_r | Limited memory, no index |
| Indexed Nested Loop | b_r + n_r × idx_cost | Index on inner join attr |
| Sort-Merge | Sort costs + b_r + b_s | Pre-sorted or need sorted output |
| Hash Join | 3(b_r + b_s) | Large equi-join, enough memory |

---

### Worked Example — Hash Join Cost (PYQ context)

**Given:** r has 5 blocks, s has 9 blocks, M = 3 buffer blocks

Check feasibility: min(b_r, b_s) / M = 5/3 ≈ 1.67 — number of partitions ≈ 2
Each r-partition size ≈ 5/2 = 2.5 blocks ≤ M-2 = 1 ← doesn't quite fit with M=3

With M=3: partitions = M-1 = 2
- Partition r: read 5, write 5 = 10 transfers
- Partition s: read 9, write 9 = 18 transfers
- Join each pair: r_0 + r_1 (2.5+2.5 = 5 blocks), read s partitions (9 blocks) = 14 transfers
- **Total ≈ 10 + 18 + 14 = 42 transfers**

(For M large enough: 3(b_r + b_s) = 3×14 = 42 — same formula)

---

## Query Optimization

### Equivalence Rules for Query Trees

These rules allow reordering operations without changing results:

| Rule | Statement |
|------|-----------|
| Cascading σ | σ_{c1 AND c2}(R) ≡ σ_{c1}(σ_{c2}(R)) |
| Commutativity of σ | σ_{c1}(σ_{c2}(R)) ≡ σ_{c2}(σ_{c1}(R)) |
| Commutativity of ⋈ | R ⋈ S ≡ S ⋈ R |
| Associativity of ⋈ | (R ⋈ S) ⋈ T ≡ R ⋈ (S ⋈ T) |
| Push σ through ⋈ | σ_c(R ⋈ S) ≡ σ_c(R) ⋈ S (if c only involves R's attrs) |
| Push π through ⋈ | π_L(R ⋈ S) ≡ π_{L∩R}(R) ⋈ π_{L∩S}(S) (approximately) |

### Heuristic Optimization Rules

Applied to the initial query tree to produce a better plan before cost estimation:

1. **Push selections down** as far as possible — reduce intermediate relation sizes early
2. **Push projections down** — reduce tuple width before joins
3. **Combine cross products with adjacent selections** to form joins
4. **Perform the most selective operation first** — minimize intermediate result sizes
5. **Avoid Cartesian products** — always try to combine with a join condition

### Cost-Based Optimization

After heuristic simplification, evaluate multiple join orderings using estimated costs:

**Statistics in the catalog:**
- `n_r`: number of tuples in r
- `b_r`: number of blocks
- `V(A, r)`: number of distinct values of attribute A in r
- Histogram of attribute distributions

**Selectivity of condition c:**
- Equality `A = v`: selectivity ≈ 1/V(A, r) → estimated result = n_r / V(A, r)
- Range `A < v`: selectivity ≈ (v - min_A) / (max_A - min_A)

### Join Order Selection

For a query joining n relations, there are n! possible orderings, and each ordering can use different algorithms.

**Left-deep join trees:** Only consider plans where the right operand of each join is a base relation (not an intermediate result). This allows pipelining — the left operand can stream results into the next join.

- For n relations: n! orderings to consider (without pruning)
- With dynamic programming and pruning: O(3^n) states

**Example:** 6 relations → 6! = 720 left-deep orderings
With dynamic programming: search space is manageable

---

## Pipelining vs Materialization

### Materialization
- Each operator fully computes its result, writes it to a temporary relation on disk, then passes it to the next operator
- Cost: extra writes and reads for intermediate results
- Simple to implement

### Pipelining
- Operators stream results directly to the next operator without materializing
- Tuple-at-a-time: each operator implements `open()`, `get_next()`, `close()` interface
- **Iterator model** (Volcano/Iterator model): pull-based, lazy evaluation
- **No disk writes** for intermediate results — significant performance gain
- Cannot be used when the same intermediate result is needed multiple times (e.g., nested-loop inner relation must be re-scanned)

---

## Materialized Views and Incremental Maintenance

A **materialized view** stores the precomputed result of a query. When the base tables change, the view must be updated.

### Incremental Maintenance

Instead of recomputing the entire view, propagate only the changes.

**For insert of tuple t into r (r is used in view v = r ⋈ s):**
```
New tuples to add to v = {t} ⋈ s
v_new = v_old ∪ ({t} ⋈ s)
```

**For delete of tuple t from r:**
```
Tuples to remove from v = {t} ⋈ s
v_new = v_old − ({t} ⋈ s)
```

**For aggregation (e.g., sum):**
- Insert: add new values to running aggregate
- Delete: subtract deleted values from aggregate
- Update: adjust aggregate by the difference

**Why materialized views?**
- Pre-compute expensive joins and aggregations
- Query reads from view → constant time instead of recomputing
- Cost: storage + update maintenance overhead

---

## Common Exam Patterns

### Factual Questions
- State the cost formula for block nested loop join. (⌈b_r/(M-2)⌉ × b_s + b_r)
- What is the cost of hash join (ideal case)? (3(b_r + b_s))
- What is the iterator model? (open/get_next/close interface; pull-based pipelining)

### Conceptual Questions
- Why do we prefer left-deep join trees over bushy trees for pipelining?
  (Right operand of each join is a base relation; can pipeline the left side without materializing)
- When does indexed nested loop join outperform hash join?
  (When the outer relation is very small and there's a good index on the inner)
- Why does secondary index equality search on a non-key attribute cost O(n_r) in the worst case?
  (Each matching record may be in a different disk block; worst case: one I/O per record)

### Solving Questions

**Given relations with block counts and memory M, compute the cheapest join algorithm:**
1. Compute NLJ cost: n_r × b_s + b_r (try both orderings)
2. Compute BNLJ cost: ⌈b_r/(M-2)⌉ × b_s + b_r (try both orderings)
3. Compute hash join cost: 3(b_r + b_s) (check feasibility)
4. If index available: b_r + n_r × (tree height + 1)
5. Choose the minimum

**Heuristic query tree transformation:**
- Given initial query tree with σ on top of ⋈
- Push σ below ⋈ to the appropriate base relation
- Draw the optimized query tree
