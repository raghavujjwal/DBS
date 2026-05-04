---
layout: default
title: Storage & File Organization
---

# Storage & File Organization

---

## Storage Hierarchy

Database data lives in a hierarchy of storage media, each with different speed, capacity, and cost trade-offs.

| Level | Type | Access Time | Capacity | Volatility |
|-------|------|-------------|----------|-----------|
| L1 Cache | SRAM | ~1 ns | KBs | Volatile |
| Main Memory | DRAM | ~100 ns | GBs | Volatile |
| Flash (SSD) | NAND Flash | ~0.1 ms | TBs | Non-volatile |
| Magnetic Disk (HDD) | Spinning disk | ~10 ms | TBs | Non-volatile |
| Magnetic Tape | Sequential tape | Seconds | PBs | Non-volatile |

**Primary storage** (cache + main memory): Fast, volatile — data lost on power failure.
**Secondary storage** (SSD + HDD): Persistent, slower — the main focus for DBMS.
**Tertiary storage** (tape): Archival, very slow.

> **Key insight for DBMS:** The bottleneck is disk I/O. Query optimization and indexing exist primarily to reduce the number of block transfers.

---

## Magnetic Disk Structure

A hard disk consists of:
- **Platters:** Circular disks coated with magnetic material
- **Tracks:** Concentric circles on each platter surface
- **Cylinders:** All tracks at the same radius across all platters
- **Sectors:** Smallest unit that can be read/written by the hardware (typically 512 bytes or 4KB)
- **Blocks (pages):** OS/DBMS unit of I/O — multiple contiguous sectors (typically 4KB–64KB)
- **Read/write head:** One per platter surface; all heads move together on the same arm

### Disk Access Time Components

```
Total access time = Seek time + Rotational delay + Transfer time
```

| Component | Description | Typical Value |
|-----------|-------------|---------------|
| **Seek time** | Time to move arm to correct cylinder | 3–15 ms avg |
| **Rotational delay** | Time to wait for sector to rotate under head | 0–T ms (avg = T/2 where T = rotation period) |
| **Transfer time** | Time to actually read/write the data | Proportional to data size |
| **Controller overhead** | Processing time in disk controller | Small, often ignored |

**Rotational speed:** 7,200 RPM → one rotation = 60/7200 = 8.33 ms → average rotational delay = 4.17 ms

**Worked example:**
- Disk: 7,200 RPM, 8 ms seek time, block size = 4 KB, transfer rate = 160 MB/s
- Rotational delay (avg) = 4.17 ms
- Transfer time for 4 KB block = 4096 / (160 × 10^6) ≈ 0.025 ms
- **Total ≈ 8 + 4.17 + 0.025 ≈ 12.2 ms per random access**

### Disk Scheduling — Elevator Algorithm (SCAN)

When multiple block requests are pending, service them in a way that minimizes total seek time.

**SCAN (Elevator) Algorithm:**
- The arm moves in one direction, servicing all requests along the way
- When it reaches the end (or runs out of requests), it reverses direction
- Name comes from elevator analogy

**SSTF (Shortest Seek Time First):** Service the request nearest to the current head position. Minimizes seek time per request but can cause starvation of distant requests.

**C-SCAN (Circular SCAN):** Only services requests in one direction; resets to the beginning after reaching the end. More uniform wait times.

---

## Flash Memory (SSD)

**NAND Flash properties:**
- Reads: Very fast, similar for random and sequential (~100 µs)
- Writes: Slower (~200 µs), and must **erase before writing**
- Erase unit: Flash **block** (128–256 pages) — you cannot overwrite a page directly; must erase the entire block first
- Wear leveling: Flash cells wear out after ~10K–100K erase cycles; wear-leveling firmware distributes writes evenly

**Why SSDs change DBMS design:**
- No seek time or rotational delay — random reads are nearly as fast as sequential
- Write amplification: a small write may require erasing and rewriting a large block
- Traditional disk-oriented B+ trees and file layouts still work well (no penalty for sequential access)

---

## Buffer Management

The DBMS buffer pool is a region of main memory used to cache disk blocks.

**Buffer replacement policies:**
- **LRU (Least Recently Used):** Evict the block that was last accessed the longest time ago — good general-purpose policy
- **MRU (Most Recently Used):** Evict the most recently used block — better for sequential scans (a block just used won't be needed again soon)
- **Clock algorithm:** Approximate LRU using a reference bit; efficient implementation
- **Pinned blocks:** Marked as unpinnable while a transaction is using them

**Dirty pages:** A buffer block that has been modified but not yet written to disk. Must be flushed to disk before eviction (or before checkpoint completion).

---

## Records and Blocks

### Record Types

| Type | Description |
|------|-------------|
| **Fixed-length** | All fields have fixed sizes; record size is constant |
| **Variable-length** | One or more fields are variable-length (VARCHAR, TEXT, BLOB) |

### Block Size and Blocking Factor

```
Record size (R) = sum of all field sizes (in bytes)
Blocking factor (bfr) = ⌊ Block size (B) / Record size (R) ⌋
Number of blocks = ⌈ Number of records (r) / bfr ⌉
```

**Example:** Block = 4096 bytes, record = 200 bytes
- bfr = ⌊4096/200⌋ = 20 records per block
- 1000 records → ⌈1000/20⌉ = 50 blocks

### Variable-Length Record Representation

**Approach 1 — Slotted page structure (used in PostgreSQL, Oracle):**
- Page header contains: record count, free space pointer, array of (offset, length) pairs
- Records packed from end; slot array grows from front
- Supports efficient free space management and record deletion

**Approach 2 — Fixed-width fields + separator:**
- Each variable-length field prefixed with its length
- Or special separator characters (e.g., NUL byte) mark field boundaries

### Spanning vs Non-spanning

- **Non-spanning:** Records never cross block boundaries; simpler but wastes space (last partial block of each file)
- **Spanning:** A record can begin in one block and continue in the next; uses space better but requires pointer to next block

---

## File Organization

### Heap (Unordered) Files

Records inserted in any order; new records go to the last block (or first block with free space).

- **Insert:** O(1) — very fast
- **Search (equality):** O(b) — must scan all blocks in worst case (b = number of blocks)
- **Delete:** Mark record as deleted (reorganize periodically)
- **Best for:** Bulk inserts, when searches use an index, when order doesn't matter

### Sorted (Sequential) Files

Records stored in sorted order on a **key field** (the ordering field).

- **Insert:** O(b) worst case — may require shifting records; usually done with overflow area + periodic reorganization
- **Search (equality on ordering field):** O(log₂ b) — binary search on blocks
- **Range search:** Excellent — ordered traversal from first matching block
- **Best for:** Reports that need sorting, range queries on the ordering key

**Binary search on sorted file:**
```
Number of block accesses = ⌈ log₂(b) ⌉
```

### Hash Files

Records stored based on the hash value of a key field; hash function maps key → bucket number → block address.

- **Search (equality on hash key):** O(1) average — compute hash, go directly to bucket
- **Insert:** O(1) average
- **Range search:** O(b) — hash destroys order; must scan all buckets
- **Delete:** Find bucket, mark as deleted
- **Best for:** Exact-match queries on the hash key

### File Organization Summary

| Operation | Heap | Sorted | Hash |
|-----------|:----:|:------:|:----:|
| Insert | Best | Worst | Good |
| Exact match | Worst | Good | Best |
| Range search | Worst | Best | Worst |
| Sequential scan | OK | Best | Worst |
| Delete | OK | Bad | Good |

---

## Record Addressing

| Method | Description |
|--------|-------------|
| **Physical address** | Disk address: (device, cylinder, track, sector, offset) |
| **Logical (relative) address** | Record number relative to file start; OS translates to physical |
| **Variable-length (pointer)** | Pointer (block address + offset within block); used in slotted pages |

**Indirection via RID (Record ID = page_number + slot_number):** Allows the physical location of a record to change (due to updates or reorganization) without invalidating pointers from indexes, as long as the slot array is updated.

---

## RAID

RAID (Redundant Array of Inexpensive Disks) improves reliability and/or performance.

| Level | Description | Benefit |
|-------|-------------|---------|
| RAID 0 | Striping only | High throughput; NO redundancy |
| RAID 1 | Mirroring | Full redundancy; doubles storage cost |
| RAID 5 | Striping + distributed parity | Balance of cost and fault tolerance (tolerates 1 disk failure) |
| RAID 6 | Striping + 2 parity blocks | Tolerates 2 simultaneous disk failures |

---

## Common Exam Patterns

### Factual Questions
- What is the blocking factor formula? (bfr = ⌊B/R⌋)
- What storage level is volatile? (Primary storage: cache and main memory)
- What is the elevator algorithm? (SCAN: service requests in one direction, reverse at end)

### Conceptual Questions
- Why is rotational delay a concern for HDDs but not SSDs?
- Explain the trade-off between spanning and non-spanning records.
- Why does hash file organization perform poorly for range queries?
- What is a slotted page structure and why is it useful?

### Solving Questions

**Block calculation:**
Given: 50,000 records, record size = 300 bytes, block size = 4096 bytes
- bfr = ⌊4096/300⌋ = 13
- Blocks = ⌈50000/13⌉ = 3847 blocks

**Access time calculation:**
Given: 5400 RPM disk, average seek = 9 ms, transfer rate = 80 MB/s, block = 8 KB
- Rotation period = 60/5400 ≈ 11.11 ms; avg rotational delay = 5.56 ms
- Transfer time = 8192/(80×10⁶) ≈ 0.10 ms
- Total random access ≈ 9 + 5.56 + 0.10 = 14.66 ms

**Binary search on sorted file:**
Given: 3847 blocks
- Accesses = ⌈log₂(3847)⌉ = ⌈11.91⌉ = 12 block accesses
