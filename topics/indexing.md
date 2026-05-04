---
layout: default
title: Indexing
---

# Indexing

---

## Why Index?

Without an index, finding a record requires scanning potentially the entire file — O(b) block accesses. An index is an auxiliary data structure that maps key values to the blocks containing those records, reducing search to O(log b) or better.

**Trade-off:** Indexes speed up search and range queries but slow down insertions and deletions (must update the index). Choose indexes based on query patterns.

---

## Index Types

### Primary vs Secondary Index

| | Primary Index | Secondary Index |
|--|--------------|----------------|
| Built on | Ordering key field of an ordered file | Any non-ordering field |
| Entry type | Sparse or dense | Usually dense |
| One index entry per | Data block (sparse) or record (dense) | Every record |
| Ordering | File must be sorted on the index key | File need not be sorted |

### Dense vs Sparse Index

| | Dense Index | Sparse Index |
|--|-------------|-------------|
| Entries | One per **record** | One per **block** |
| Points to | Individual record | First record in the block |
| Space | More | Less |
| Useful for | Non-ordered files, secondary indexes | Ordered files (primary) |
| Requires sorted file? | No | Yes |

---

## Primary Index (Sparse — one entry per data block)

A **primary index** is built on the ordering key field of a sorted data file. One index entry per data block — points to the first record (anchor record) in each block.

```
Index entry = (key value of anchor record, pointer to block)
Index entry size = K + P  (key size + pointer size)
```

**Building the index:**
```
Index entries = number of data blocks (b)
bfr_i = ⌊B / (K + P)⌋      (index blocking factor)
Index blocks = ⌈b / bfr_i⌉
```

**Search algorithm:**
1. Binary search on the index to find the block containing the target key
2. Fetch that one data block → 1 more access
3. Total accesses = ⌈log₂(index blocks)⌉ + 1

**Worked example:**
- File: 50,000 records, R = 300 bytes, B = 4096 → bfr = 13 → data blocks = 3847
- Index: K = 9 bytes, P = 6 bytes → entry = 15 bytes → bfr_i = 273 → index blocks = 15
- Search: ⌈log₂(15)⌉ + 1 = 4 + 1 = **5 block accesses** (vs 12 for binary search on data file)

---

## Clustering Index (on non-key ordering field)

When a file is sorted on a non-key field (multiple records can have the same value), a **clustering index** has one entry per distinct value of the field, pointing to the first block with that value.

---

## Secondary Index (Dense — one entry per record)

Built on a non-ordering field. Must be dense because records with the same secondary key value are scattered throughout the file.

```
Index entries = number of records (r)
Index blocks = ⌈r / bfr_i⌉
```

**Problem:** For a non-key secondary index, multiple records share the same index key value. Two approaches:
1. **Duplicate index entries:** One entry per record with that key value (wastes index space)
2. **Bucket pointer:** Index entry points to a bucket of record pointers — space efficient

---

## Multi-Level Index

An index on an index. When the first-level index is too large to binary search efficiently, build another (smaller) index on top.

```
Level 1: ⌈r / bfr_i⌉ blocks   (or ⌈b / bfr_i⌉ for sparse)
Level 2: ⌈Level 1 blocks / bfr_i⌉ blocks
...
Stop when level k has exactly 1 block
Total accesses = number of levels + 1 (for data block)
```

**Number of levels:** t = ⌈log_{bfr_i}(r)⌉ for dense, ⌈log_{bfr_i}(b)⌉ for sparse

A multi-level index with a branching factor of bfr_i is essentially a static B-tree.

---

## B+ Trees

The standard dynamic index structure used in almost all production databases.

### Structure

- **Internal (non-leaf) nodes:** Contain keys only — used for navigation/search
- **Leaf nodes:** Contain keys + pointers to actual data records; linked in a chain for range queries
- **Root:** Special case — can have as few as 1 key (2 children) if non-leaf, or 1 key if leaf

### Order (Degree) n

```
Non-leaf node: can hold up to n-1 keys and n child pointers
  Constraint: n × P + (n-1) × K ≤ B
  Solve for n: n = ⌊(B + K) / (K + P)⌋

Leaf node: holds up to n-1 (key, record-pointer) pairs + 1 sibling pointer
  Constraint: (n-1) × (K + P_data) + P_sibling ≤ B
  Solve for n-1: n-1 = ⌊(B - P_sibling) / (K + P_data)⌋
```

Where K = key size, P = node pointer size, P_data = data record pointer size.

### Capacity Constraints (for exam — critical)

| Node Type | Minimum | Maximum |
|-----------|---------|---------|
| Non-leaf keys | ⌈n/2⌉ - 1 | n - 1 |
| Non-leaf children | ⌈n/2⌉ | n |
| Leaf keys | ⌈(n-1)/2⌉ | n - 1 |
| Root (non-leaf) | 1 key, 2 children | n - 1 keys, n children |
| Root (leaf, i.e., only node) | 0 keys | n - 1 keys |

### Height

For N records:
```
Height h satisfies:
  min records: first level has 2 children, each ≥ ⌈n/2⌉ children, ..., leaves ≥ ⌈(n-1)/2⌉ keys
  max height: h ≤ ⌊log_{⌈n/2⌉}(N)⌋ + 1

Typical search: h block accesses (one per level) + 1 for data block = h+1 total
```

### Insertion

1. Search to find the correct leaf node
2. If leaf has room, insert key in sorted order
3. If leaf is full (**overflow**): **split** the leaf
   - Move ⌈(n-1)/2⌉ entries to a new leaf (right half)
   - Copy the **smallest key of the new leaf** up to the parent
   - Update leaf chain pointers
4. If parent is also full: split the internal node
   - Move ⌈n/2⌉ - 1 keys to new node
   - **Push** the middle key up to the parent (key is removed from leaf level — "pushed", not "copied")
5. Splits propagate upward; root split increases tree height

**Key distinction:**
- Leaf splits: key is **copied** up (stays in leaf + also in parent)
- Internal node splits: key is **pushed** up (removed from current level, goes to parent only)

### Deletion

1. Find and remove the key from the leaf
2. If leaf has ≥ ⌈(n-1)/2⌉ keys → done
3. If underflow:
   - **Redistribute:** Try to borrow a key from a sibling leaf (via the parent) — update parent key
   - **Merge:** If no sibling can lend, merge the leaf with a sibling; the corresponding parent key is deleted
4. Internal node underflow handled similarly (redistribute or merge)
5. Merges can propagate upward; root can lose its last key if both children merge (tree height decreases)

### Bulk Loading

Inserting N records one at a time into a B+ tree is expensive due to repeated splits and cache misses. **Bulk loading:**
1. Sort all records by the index key
2. Fill leaf nodes sequentially to a desired fill factor (e.g., 70%)
3. Build internal nodes bottom-up as leaves are created

**Issue with standard bulk loading:** If data is loaded nearly full and subsequent insertions happen with sequential keys, the right-most leaf node is always split (right-biased splits). This creates poorly utilized leaves on the left side. Solution: leave some free space during loading or use a fill factor < 100%.

---

## Extendible Hashing

A dynamic hashing scheme that grows without overflow chains.

### Structure
- **Directory:** An array of 2^d pointers (d = global depth), stored in memory
- **Buckets:** Disk pages, each with a **local depth** ld ≤ d
- Multiple directory entries can point to the same bucket

### Lookup
```
hash(K) → h (use high-order OR low-order bits, per convention)
Use the first d bits of h to index into the directory
Follow the pointer to the bucket
```

### Overflow Handling
When a bucket with local depth ld overflows:
- If **ld < d (global depth):** Split the bucket only; double the number of directory entries pointing to this bucket; redistribute records; increment ld for both halves
- If **ld = d:** First **double the directory** (d becomes d+1; each old entry spawns two new entries pointing to the same bucket); then split the overflowing bucket

### Space
- Directory size: 2^d entries
- After n splits from an initial directory of size 1: directory can grow to 2^n entries
- Buckets: proportional to actual number of records

---

## Linear Hashing

Another dynamic hashing scheme, but avoids doubling the directory.

### Key Idea
- Maintain a **split pointer** s (initially 0) and **level** l (initially 0)
- Total buckets at any time: n × 2^l + s (where n = initial number of buckets)

### Hash Functions
```
h_l(K) = K mod (n × 2^l)          — current-level hash
h_{l+1}(K) = K mod (n × 2^{l+1}) — next-level hash (for "already split" buckets)
```

### Lookup
```
bucket = h_l(K)
if bucket < s (split pointer):
  use h_{l+1}(K) instead
```

### Splitting
- Overflow anywhere → split the bucket at the **split pointer** (NOT the overflowing bucket)
- Create a new bucket; redistribute records in the split-pointer bucket using h_{l+1}
- Increment s
- If s = n × 2^l: set l = l + 1, reset s = 0

### Properties
- No directory doubling — size grows linearly with number of records
- Split order is predetermined, not triggered by where overflow occurs
- May have overflow chains (handled by chaining buckets)

---

## Multidimensional Indexing

### Problem
Traditional B+ trees and hash indexes work on a single attribute. For spatial/multidimensional queries (range queries on lat/lon, k-NN search), we need multi-attribute indexes.

### Grid File
- Partition each dimension independently using linear scale arrays
- Each cell in the grid → a bucket of records
- **Point query:** Determine which grid cell contains the point; access that bucket — O(1)
- **Range query:** Find all overlapping cells; access each bucket
- **Nearest neighbor:** Find closest candidate; then search all buckets whose closest point to the query is ≤ current best distance

### R-Tree

A tree structure for spatial objects (points, rectangles, polygons). Each node contains **MBRs** (Minimum Bounding Rectangles) of its children.

**Search:** Traverse the tree, following subtrees whose MBR overlaps the query region.

**Nearest Neighbor Search using MINDIST:**
```
For query point Q = (qx, qy) and MBR [xmin, xmax] × [ymin, ymax]:

dx = xmin - qx  if qx < xmin
     0           if xmin ≤ qx ≤ xmax
     qx - xmax   if qx > xmax

dy = (same logic for y-dimension)

MINDIST(Q, MBR) = √(dx² + dy²)
```

**Pruning rule:** If MINDIST(Q, node's MBR) > current best distance, prune the entire subtree.

**Algorithm:**
1. Start at root; maintain a priority queue of (MINDIST, node)
2. Dequeue the node with smallest MINDIST
3. If leaf: check actual records; update best if closer
4. If internal: enqueue all children with their MINDIST values
5. Prune any queued node with MINDIST > current best

---

## Common Exam Patterns

### Factual Questions
- How many levels does a multi-level index have? (⌈log_{bfr_i}(b)⌉ for sparse)
- What is the order n formula for a B+ tree non-leaf node? (n = ⌊(B+K)/(K+P)⌋)
- What is MINDIST in R-tree NN search? (minimum possible distance from query point to any point in the MBR)

### Conceptual Questions
- When would you choose a sparse primary index over a dense index? (When file is sorted on the index key; sparse uses less space and still enables binary search)
- Compare extendible hashing and linear hashing on: directory growth, overflow handling, and lookup cost.
- Why does leaf splitting in B+ trees copy a key up while internal node splitting pushes?

### Solving Questions

**Index size and search cost calculation** (very common in exams):
1. Compute data blocking factor
2. Compute number of data blocks
3. Compute index entry size
4. Compute index blocking factor
5. Compute number of index blocks
6. Compute search accesses = ⌈log₂(index blocks)⌉ + 1

**B+ tree insertion/deletion:**
- Draw the tree before the operation
- Follow split/merge rules precisely
- Show the final tree state

**Extendible hashing:**
- Track global depth, local depths, directory size
- Perform insertions; show directory doubling when needed
- Show final directory and bucket states

**Linear hashing:**
- Track level l, split pointer s, number of buckets
- Compute correct hash function based on whether bucket < s
- Show splits triggered by overflow
