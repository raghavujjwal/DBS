---
layout: default
title: Quick Reference
---

# Quick Reference Card

Formulas, algorithms, and key facts for last-minute revision.

---

## Storage & File Organization

### Record and Block Calculations

```
Record size (R) = sum of all field sizes
Blocking factor (bfr) = ⌊Block size (B) / Record size (R)⌋
Number of blocks = ⌈Number of records (r) / bfr⌉
Binary search accesses = ⌈log₂(number of blocks)⌉
```

### Primary Index

```
Index entry size = Key size + Pointer size
Index blocking factor (bfr_i) = ⌊B / index entry size⌋
First-level index entries = number of data blocks (for sparse) or records (for dense)
First-level index blocks = ⌈index entries / bfr_i⌉
```

### Multi-Level Index

```
Level 1 blocks = ⌈first-level entries / bfr_i⌉
Level k blocks = ⌈Level (k-1) blocks / bfr_i⌉
Stop when level has 1 block
Total accesses = number of levels + 1 (for data block)
```

### File Organization — When to Use

| Operation | Heap | Sorted | Hash |
|-----------|------|--------|------|
| Insert | Best | Worst | Good |
| Exact match search | Worst | Good | Best |
| Range search | Worst | Best | Worst |
| Sequential scan | OK | Best | Worst |
| Delete | OK | Bad | Good |

### Variable-Length Records — Two Approaches

1. **Slotted page structure:** Header has count + free space pointer + slot array pointing to records
2. **Pointer-based:** Separator characters or length fields before each variable-length field

---

## B+ Tree

### Degree Calculation

```
For non-leaf: (n-1) keys + n pointers fit in one block
  n × P + (n-1) × K ≤ B
  n = ⌊(B + K) / (K + P)⌋

For leaf: (n-1) keys + (n-1) data pointers + 1 sibling pointer
  (n-1) × (K + P_data) + P_sibling ≤ B
  n-1 = ⌊(B - P) / (K + P_data)⌋
```

### Capacity

```
Non-leaf: min ⌈n/2⌉ children, max n children
Leaf: min ⌈(n-1)/2⌉ keys, max (n-1) keys
Root: min 2 children (if non-leaf), min 1 key (if leaf)
```

### Height Bounds

```
Max height for N records:
  h ≤ ⌈log_⌈n/2⌉(N / ⌈(n-1)/2⌉)⌉ + 1

Accesses for search = height + 1 (for data block)
```

### Key Properties

- Height-balanced
- Leaf nodes are linked (supports range queries)
- Only leaf nodes have data pointers
- Non-leaf nodes are directory — guide search only
- Insertion may cause splits; deletion may cause merges or redistribution

---

## Hashing

### Extendible Hashing

- Directory of size 2^(global depth)
- Each bucket has a local depth
- On overflow: if local depth < global depth → split bucket only; if local depth = global depth → double directory, then split
- Use high-order bits (or low-order depending on convention)

### Linear Hashing

- Level starts at 0, split pointer starts at 0
- Hash function: h_level(K) = K mod (2^level × N)
- Next function: h_(level+1)(K) = K mod (2^(level+1) × N)
- If h_level(K) < split pointer, use h_(level+1)
- Split triggered by overflow; split the bucket at the split pointer (not necessarily the overflowing one)

---

## Normalization

### Armstrong's Axioms

| Rule | Statement |
|------|-----------|
| Reflexivity | If Y ⊆ X, then X → Y |
| Augmentation | If X → Y, then XZ → YZ |
| Transitivity | If X → Y and Y → Z, then X → Z |
| Union | If X → Y and X → Z, then X → YZ |
| Decomposition | If X → YZ, then X → Y and X → Z |
| Pseudo-transitivity | If X → Y and WY → Z, then WX → Z |

### Closure Algorithm

```
Input: Set of FDs F, attribute set X
Output: X⁺

X⁺ = X
repeat:
  for each FD (Y → Z) in F:
    if Y ⊆ X⁺:
      X⁺ = X⁺ ∪ Z
until X⁺ does not change
```

### Finding Candidate Keys

1. Find attributes that appear only on LHS of FDs → must be in every key
2. Find attributes not in any FD → must be in every key
3. Compute closure of these attributes
4. If closure = all attributes → this is the only candidate key
5. Otherwise, try adding other attributes one at a time

### Normal Form Decision Tree

```
Check BCNF: For every non-trivial FD X → A, is X a superkey?
  Yes → BCNF (also 3NF, 2NF, 1NF)
  No → Check 3NF: Is A a prime attribute?
    Yes for all violating FDs → 3NF
    No → Check 2NF: Is there partial dependency?
      No partial dependency → 2NF
      Partial dependency exists → only 1NF
```

### Canonical (Minimal) Cover Algorithm

1. **Decompose RHS:** Split every FD with multiple attributes on RHS
2. **Remove extraneous LHS attributes:** For each FD X → A where |X| > 1, try removing each attribute from X. If closure still contains A → remove it
3. **Remove redundant FDs:** For each FD, check if it can be derived from the remaining FDs. If yes → remove it

### Lossless Join Test (Binary)

```
R decomposed into R1 and R2 is lossless iff:
  (R1 ∩ R2) → R1    OR    (R1 ∩ R2) → R2
i.e., common attributes form a superkey of at least one sub-relation
```

### 4NF

```
For every non-trivial MVD X →→ Y:
  X must be a superkey

MVD semantics: "For a given X value, the set of Y values 
is independent of the set of (R - X - Y) values"
```

---

## Concurrency Control

### Lock Compatibility Matrix

|  | S | X |
|--|---|---|
| **S** | Yes | No |
| **X** | No | No |

### 2PL Variants

| Variant | Growing Phase | Shrinking Phase | Guarantees |
|---------|--------------|-----------------|------------|
| Simple 2PL | Acquire locks | Release locks | Conflict serializability |
| Strict 2PL | Acquire locks | Release X-locks at commit | + No cascading rollback |
| Rigorous 2PL | Acquire locks | Release ALL locks at commit | + Recoverability |
| Conservative 2PL | Acquire ALL locks before start | Release anytime | + Deadlock freedom |

### Timestamp Protocol Rules

```
Read(X) by T:
  if TS(T) < W-TS(X): REJECT (rollback T, restart with new TS)
  else: allow, set R-TS(X) = max(R-TS(X), TS(T))

Write(X) by T:
  if TS(T) < R-TS(X): REJECT (rollback T)
  if TS(T) < W-TS(X): Thomas Write Rule — skip write (don't reject)
  else: allow, set W-TS(X) = TS(T)
```

### Protocol Comparison

| Property | Simple 2PL | Strict 2PL | Rigorous 2PL | Timestamp |
|----------|:----------:|:----------:|:------------:|:---------:|
| Conflict serializable | Yes | Yes | Yes | Yes |
| View serializable | Yes | Yes | Yes | Yes |
| Deadlock free | No | No | No | Yes |
| Recoverable | No | Yes | Yes | No |
| Cascadeless | No | Yes | Yes | No |

---

## Serializability

### Conflict Serializability Test

1. Draw precedence graph: node per transaction
2. Add edge Ti → Tj if Ti has an operation that conflicts with a later operation of Tj
3. Conflicts: R-W, W-R, W-W on same data item
4. **Acyclic graph → conflict serializable** (topological sort gives serial order)
5. **Cycle → NOT conflict serializable**

### View Serializability Conditions

For schedule S to be view equivalent to serial schedule S':
1. Same initial reads: if Ti reads initial value of X in S, Ti must read initial value of X in S'
2. Same reads-from: if Ti reads X written by Tj in S, same must hold in S'
3. Same final writes: if Ti performs final write of X in S, same in S'

### Recoverability & Cascadelessness

```
Recoverable: If Ti reads from Tj, then commit(Tj) < commit(Ti)
Cascadeless: Ti reads X only after the Tj that last wrote X has committed
Strict: Ti cannot read or write X until writer Tj commits/aborts

Strict ⊂ Cascadeless ⊂ Recoverable ⊂ All schedules
```

---

## Crash Recovery

### Log Record Format

```
[START Ti]
[Ti, X, old_value, new_value]
[COMMIT Ti]
[ABORT Ti]
[START CKPT (list of active transactions)]
[END CKPT]
```

### Recovery with Immediate Update

1. Scan log backwards from crash point to find latest checkpoint
2. **Undo list:** Transactions that started but did NOT commit before crash
3. **Redo list:** Transactions that committed before crash (after checkpoint start)
4. **Undo phase:** Scan backwards, undo all operations of undo-list transactions (restore old values)
5. **Redo phase:** Scan forwards, redo all operations of redo-list transactions (apply new values)

### WAL (Write-Ahead Logging)

- Log record must be written to stable storage BEFORE the corresponding data page is written to disk
- On recovery: committed transactions are redone, uncommitted are undone

### Shadow Paging

- Maintain current page table and shadow page table
- On commit: make current table the shadow
- On crash: shadow table is the valid state
- **Not suitable for DBMS:** poor support for concurrent transactions, high overhead for large DBs
- **Suitable for:** file systems (e.g., ext4 journaling)

---

## Query Processing

### Join Algorithms — Cost (in block transfers)

| Algorithm | Cost | When to Use |
|-----------|------|-------------|
| Nested Loop | n_r × b_s + b_r | Small outer relation |
| Block Nested Loop | b_r × b_s / (M-2) + b_r | Limited memory |
| Indexed Nested Loop | b_r + n_r × cost_of_index_lookup | Index on inner |
| Sort-Merge Join | b_r + b_s + sort costs | Both sortable |
| Hash Join | 3(b_r + b_s) | Equi-join, fits memory |

Where: b = blocks, n = records, M = memory blocks available

### Heuristic Optimization Rules

1. Push selections as early as possible (reduce intermediate sizes)
2. Push projections as early as possible (reduce tuple width)
3. Combine Cartesian products with selections to form joins
4. Choose the most restrictive selection first
5. Avoid Cartesian products if possible

---

## Multidimensional Indexing

### MINDIST for R-tree NN Query

```
For query point Q = (qx, qy) and MBR [xmin, xmax, ymin, ymax]:

dx = xmin - qx   if qx < xmin
     0            if xmin ≤ qx ≤ xmax
     qx - xmax    if qx > xmax

(same for dy)

MINDIST = √(dx² + dy²)

If MINDIST > current best distance → prune this subtree
```

### Grid File NN Query

For nearest neighbor at point P with closest candidate Q at distance d:
- Must search all buckets whose closest point to P is ≤ d
- Number of I/Os = number of such buckets (assuming no overflow chains)

---

## ER Mapping Rules — Quick Reference

| ER Construct | Mapping Strategy |
|-------------|-----------------|
| Strong entity | Table with all simple attributes, PK = entity key |
| Weak entity | Table with own attributes + owner's PK as FK; PK = partial key + owner PK |
| 1:1 relationship | FK in the entity with total participation (or either, if both total) |
| 1:N relationship | FK in the N-side entity |
| M:N relationship | New junction table with PKs of both entities |
| Multi-valued attribute | Separate table with FK to owning entity |
| Composite attribute | Flatten — use simple components only |
| Derived attribute | Do not store (or store with trigger to maintain) |

### 1:1 Mapping Decision Guide

| Participation | Strategy |
|--------------|----------|
| Total on both sides | Merge into one table |
| Total on one side | FK in the total-participation side |
| Optional on both | FK in either side (choose the one with more participation) |
