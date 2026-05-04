---
layout: default
title: Topic-wise Study Guide
---

# Topic-wise Study Guide

Full lecture content, question patterns, and worked examples for every topic. Each page contains theory from the BITS Pilani slides, exam-focused question breakdowns, and solving strategies.

<div class="legend">
  <span class="badge badge-factual">Factual</span> Memorize
  <span class="badge badge-conceptual">Conceptual</span> Understand
  <span class="badge badge-solving">Solving</span> Practice
</div>

---

## Module 1 — Data Modeling

<div class="card-grid">

<a href="{{ '/topics/er-model' | relative_url }}" class="card">
  <h3>ER Model & Mapping</h3>
  <p>Entities, attributes, relationships, cardinality, weak entities, specialization. Full mapping rules for 1:1, 1:N, M:N, weak entities, multi-valued attributes.</p>
  <div>
    <span class="badge badge-factual">Factual</span>
    <span class="badge badge-solving">Solving</span>
  </div>
</a>

<a href="{{ '/topics/relational-model' | relative_url }}" class="card">
  <h3>Relational Model & Query Languages</h3>
  <p>Codd's model, keys, integrity constraints, NULL and 3-valued logic, relational algebra operators, outer joins, relational calculus, Codd's theorem.</p>
  <div>
    <span class="badge badge-factual">Factual</span>
    <span class="badge badge-conceptual">Conceptual</span>
    <span class="badge badge-solving">Solving</span>
  </div>
</a>

</div>

---

## Module 2 — Schema Design & Normalization

<div class="card-grid">

<a href="{{ '/topics/normalization' | relative_url }}" class="card">
  <h3>Normalization & FDs</h3>
  <p>Update anomalies, functional dependencies, Armstrong's axioms, closure algorithm, candidate key finding, 1NF–BCNF, lossless join, dependency preservation, canonical cover, 3NF synthesis.</p>
  <div>
    <span class="badge badge-factual">Factual</span>
    <span class="badge badge-conceptual">Conceptual</span>
    <span class="badge badge-solving">Solving</span>
  </div>
</a>

<a href="{{ '/topics/mvd-4nf' | relative_url }}" class="card">
  <h3>MVDs & 4NF</h3>
  <p>Multi-valued dependencies, the CTX motivation, Fagin's theorem, trivial MVDs, 4NF definition, 4NF decomposition algorithm. Why BCNF is not enough.</p>
  <div>
    <span class="badge badge-conceptual">Conceptual</span>
    <span class="badge badge-solving">Solving</span>
  </div>
</a>

</div>

---

## Module 3 — Storage & Indexing

<div class="card-grid">

<a href="{{ '/topics/storage' | relative_url }}" class="card">
  <h3>Storage & File Organization</h3>
  <p>Storage hierarchy, disk mechanics, access time calculation, elevator algorithm, flash memory, blocking factor formula, heap/sorted/hash files, slotted pages, RAID.</p>
  <div>
    <span class="badge badge-factual">Factual</span>
    <span class="badge badge-solving">Solving</span>
  </div>
</a>

<a href="{{ '/topics/indexing' | relative_url }}" class="card">
  <h3>Indexing</h3>
  <p>Primary/secondary/sparse/dense indexes, multi-level index, B+ tree structure, degree calculation, insertion/deletion, bulk loading. Extendible hashing, linear hashing, R-tree MINDIST for NN queries.</p>
  <div>
    <span class="badge badge-factual">Factual</span>
    <span class="badge badge-conceptual">Conceptual</span>
    <span class="badge badge-solving">Solving</span>
  </div>
</a>

</div>

---

## Module 4 — Query Processing

<div class="card-grid">

<a href="{{ '/topics/query-processing' | relative_url }}" class="card">
  <h3>Query Processing & Optimization</h3>
  <p>Query processing pipeline, selection algorithms, external merge sort, all 5 join algorithms with cost formulas, equivalence rules, heuristic optimization, cost-based optimization, pipelining vs materialization.</p>
  <div>
    <span class="badge badge-factual">Factual</span>
    <span class="badge badge-conceptual">Conceptual</span>
    <span class="badge badge-solving">Solving</span>
  </div>
</a>

</div>

---

## Module 5 — Transaction Management & Recovery

<div class="card-grid">

<a href="{{ '/topics/transactions' | relative_url }}" class="card">
  <h3>Transaction Management & Concurrency</h3>
  <p>ACID properties, transaction states, lost update / dirty read / inconsistent analysis, conflict serializability, precedence graph, view serializability, recoverability, 2PL variants, timestamp ordering, protocol comparison.</p>
  <div>
    <span class="badge badge-factual">Factual</span>
    <span class="badge badge-conceptual">Conceptual</span>
    <span class="badge badge-solving">Solving</span>
  </div>
</a>

<a href="{{ '/topics/crash-recovery' | relative_url }}" class="card">
  <h3>Crash Recovery</h3>
  <p>Log records, immediate vs deferred update, WAL protocol, checkpointing, undo/redo classification, worked recovery examples, shadow paging, compensating transactions.</p>
  <div>
    <span class="badge badge-factual">Factual</span>
    <span class="badge badge-conceptual">Conceptual</span>
    <span class="badge badge-solving">Solving</span>
  </div>
</a>

</div>

---

## Quick Navigation

| Module | Topics |
|--------|--------|
| Data Modeling | [ER Model & Mapping]({{ '/topics/er-model' | relative_url }}) · [Relational Model]({{ '/topics/relational-model' | relative_url }}) |
| Schema Design | [Normalization & FDs]({{ '/topics/normalization' | relative_url }}) · [MVDs & 4NF]({{ '/topics/mvd-4nf' | relative_url }}) |
| Storage & Indexing | [Storage & File Organization]({{ '/topics/storage' | relative_url }}) · [Indexing]({{ '/topics/indexing' | relative_url }}) |
| Query Processing | [Query Processing & Optimization]({{ '/topics/query-processing' | relative_url }}) |
| Transactions | [Transaction Management]({{ '/topics/transactions' | relative_url }}) · [Crash Recovery]({{ '/topics/crash-recovery' | relative_url }}) |
