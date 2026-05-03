---
layout: default
title: Home
---

# CS F212 — Database Systems Study Guide

A comprehensive exam preparation guide built by analyzing **4 years of DBS comprehensive examination papers** (2017-18, 2021-22, 2022-23, 2023-24) from BITS Pilani. Questions are categorized into three types to help you study strategically.

<div class="legend">
  <span class="badge badge-factual">Factual</span> — Recall definitions, properties, or standard facts
  <span class="badge badge-conceptual">Conceptual</span> — Explain why, compare, or reason about behavior
  <span class="badge badge-solving">Solving</span> — Compute, construct, or work through an algorithm
</div>

---

## Exam Pattern Summary

| Year | Format | Closed Book | Open Book | Total Marks |
|------|--------|-------------|-----------|-------------|
| 2023-24 | 20 MCQ + 10 Short Answer | 60 min | — | 40 |
| 2022-23 | Part A (closed) + Part B (open) | 30 min | 85 marks | 105 |
| 2021-22 | Part A (closed MCQ) + Part B (open) | 30 min | 105 marks | 120 |
| 2017-18 | Part A (closed short) + Part B+C (open) | 45 min | 60 marks | 80 |

> The exam consistently has a **closed-book section** testing recall and quick reasoning, and an **open-book section** requiring detailed problem solving. Negative marking applies to MCQs (typically 25-50%).

---

## Topic Frequency Across Papers

| Topic | 17-18 | 21-22 | 22-23 | 23-24 | Frequency |
|-------|:-----:|:-----:|:-----:|:-----:|:---------:|
| Normalization (FDs, NFs, Decomposition) | ✓ | ✓ | ✓ | ✓ | <span class="badge badge-high">4/4</span> |
| B+ Tree Indexing | ✓ | ✓ | ✓ | ✓ | <span class="badge badge-high">4/4</span> |
| Transaction Management & Concurrency | ✓ | ✓ | ✓ | ✓ | <span class="badge badge-high">4/4</span> |
| Crash Recovery (Logs, Checkpoints) | — | ✓ | ✓ | ✓ | <span class="badge badge-high">3/4</span> |
| Hashing (Extendible, Linear) | ✓ | ✓ | — | — | <span class="badge badge-medium">2/4</span> |
| File Organization & Storage | ✓ | ✓ | ✓ | ✓ | <span class="badge badge-high">4/4</span> |
| Relational Algebra & SQL | ✓ | — | ✓ | ✓ | <span class="badge badge-high">3/4</span> |
| ER Model & Mapping | — | — | — | ✓ | <span class="badge badge-low">1/4</span> |
| Multidimensional Indexing (Grid, R-tree) | ✓ | — | — | — | <span class="badge badge-low">1/4</span> |
| Query Processing & Optimization | ✓ | ✓ | — | — | <span class="badge badge-medium">2/4</span> |
| Materialized Views | ✓ | — | — | — | <span class="badge badge-low">1/4</span> |
| 4NF / Multivalued Dependencies | — | — | ✓ | ✓ | <span class="badge badge-medium">2/4</span> |

---

## Question Type Distribution

Based on analysis of all 4 papers:

| Type | Approx. % | Where It Appears |
|------|-----------|------------------|
| <span class="badge badge-factual">Factual</span> | ~25% | Mostly closed-book MCQs |
| <span class="badge badge-conceptual">Conceptual</span> | ~30% | Closed-book short answers, open-book justifications |
| <span class="badge badge-solving">Solving</span> | ~45% | Dominates open-book section |

---

## Navigation

<div class="card-grid">
  <a href="{{ site.baseurl }}/topics/" class="card" style="text-decoration:none">
    <h3>Topic-wise Study Guide</h3>
    <p>Every topic with question-type breakdown, what to study, and practice patterns</p>
  </a>
  <a href="{{ site.baseurl }}/pyq/" class="card" style="text-decoration:none">
    <h3>PYQ Breakdown</h3>
    <p>Year-by-year question analysis with difficulty and type tags</p>
  </a>
  <a href="{{ site.baseurl }}/reference/" class="card" style="text-decoration:none">
    <h3>Quick Reference</h3>
    <p>Formulas, algorithms, and key facts for last-minute revision</p>
  </a>
</div>
