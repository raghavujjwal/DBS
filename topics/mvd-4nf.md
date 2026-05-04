---
layout: default
title: MVDs & 4NF
---

# Multi-Valued Dependencies & 4NF

---

## Motivation — Why Normal Forms Beyond BCNF?

Even a relation in BCNF can exhibit redundancy and anomalies when it stores **independent multi-valued facts** in a single relation.

### The CTX Relation

Consider the relation:
```
CTX(course, teacher, textbook)
```
with the following business rule:
- Each **course** is taught by a set of **teachers** (independent of textbooks)
- Each **course** uses a set of **textbooks** (independent of teachers)
- A given teacher teaches a course with **all** textbooks for that course (and vice versa)

| course | teacher | textbook |
|--------|---------|---------|
| Physics | Dr. A | Halliday |
| Physics | Dr. A | Serway |
| Physics | Dr. B | Halliday |
| Physics | Dr. B | Serway |

**Observation:**
- Adding a new teacher (Dr. C) for Physics requires adding one row per textbook
- Adding a new textbook requires adding one row per teacher
- There is NO functional dependency between teacher and textbook — neither determines the other
- Yet **massive redundancy** exists: every (teacher, textbook) combination for a course must be stored

This redundancy stems from a **multi-valued dependency**, not a functional dependency. BCNF cannot detect or remove it.

---

## Multi-Valued Dependencies (MVDs)

### Definition

A **multi-valued dependency (MVD)** X →→ Y holds in relation R if, for any two tuples t1, t2 in R with t1[X] = t2[X], there exists a tuple t3 in R such that:
- t3[X] = t1[X] = t2[X]
- t3[Y] = t1[Y]
- t3[R - X - Y] = t2[R - X - Y]

**Intuitive meaning:** The set of Y values associated with a given X value is **independent** of the set of (R - X - Y) values.

> "For a given X, the Y-values and Z-values (where Z = R - X - Y) are independent — every Y value appears with every Z value."

**Example:** In CTX:
- `course →→ teacher` — for a given course, the set of teachers is independent of the textbooks
- `course →→ textbook` — for a given course, the set of textbooks is independent of the teachers

### Formal Notation
```
X →→ Y    read as "X multi-determines Y"
```

### MVD Symmetry Property

If X →→ Y holds in R(X, Y, Z) where Z = R - X - Y, then **X →→ Z** also holds.

MVDs always come in complementary pairs!

---

## Trivial MVDs

An MVD X →→ Y is **trivial** if:
- Y ⊆ X, OR
- X ∪ Y = R (Y contains all remaining attributes)

Trivial MVDs hold in every relation and carry no semantic content.

### Every FD is also an MVD

If X → Y (functional dependency), then X →→ Y (multi-valued dependency).
- FDs are special cases of MVDs where the set of Y values has exactly one element.

---

## Fagin's Theorem (Decomposition Test)

A relation R(X, Y, Z) can be decomposed **losslessly** into R1(X, Y) and R2(X, Z) **if and only if** X →→ Y holds in R (equivalently, X →→ Z holds).

This is the foundation for 4NF decomposition.

---

## 4NF (Fourth Normal Form)

### Definition

A relation R is in **4NF** with respect to a set of FDs and MVDs F if, for every non-trivial MVD X →→ Y in R:
- **X is a superkey of R**

### Why this condition?

If X →→ Y is non-trivial and X is NOT a superkey, then R stores independent multi-valued facts for multiple X values — causing the exact redundancy we saw in CTX.

### CTX Example — Checking 4NF

In CTX(course, teacher, textbook):
- `course →→ teacher` is non-trivial (teacher ⊄ course, and course ∪ teacher ≠ CTX)
- Is `course` a superkey? No — course alone doesn't uniquely identify tuples
- **CTX is NOT in 4NF**

### 4NF Decomposition

Decompose using the violating MVD:

```
CTX(course, teacher, textbook)
  with MVD: course →→ teacher (equivalently: course →→ textbook)

Decompose into:
  CT(course, teacher)   ← course →→ teacher is now trivial (course ∪ teacher = CT)
  CX(course, textbook)  ← course →→ textbook is now trivial
```

**Result:**
```
CT(course, teacher)
  | course  | teacher |
  |---------|---------|
  | Physics | Dr. A   |
  | Physics | Dr. B   |

CX(course, textbook)
  | course  | textbook |
  |---------|----------|
  | Physics | Halliday |
  | Physics | Serway   |
```

Adding Dr. C → one row in CT only.
Adding a new textbook → one row in CX only.
No redundancy!

---

## Comparing Normal Forms

```
1NF ⊃ 2NF ⊃ 3NF ⊃ BCNF ⊃ 4NF
```

Each is a strictly stronger condition than the previous.

| Normal Form | Eliminates |
|-------------|-----------|
| 2NF | Partial dependencies |
| 3NF | Transitive dependencies (while preserving FDs) |
| BCNF | All FD-based redundancy |
| 4NF | MVD-based redundancy (independent multi-valued facts) |

---

## MVD Rules (Inference Axioms)

| Rule | Statement |
|------|-----------|
| **Complementation** | If X →→ Y in R, then X →→ (R - X - Y) |
| **Augmentation** | If X →→ Y and W ⊇ Z, then XZ →→ YW |
| **Transitivity** | If X →→ Y and Y →→ Z, then X →→ (Z - Y) |
| **Replication** | If X → Y, then X →→ Y |
| **Coalescence** | If X →→ Y, Y ⊆ W, W ∩ Y = ∅, W → Z, Z ⊆ Y, then X → Z |

---

## 4NF Decomposition Algorithm

```
Given relation R and FDs/MVDs F:
result = {R}
while some relation Ri is not in 4NF:
  find a non-trivial MVD X →→ Y in Ri where X is not a superkey of Ri
  decompose Ri into:
    R1 = X ∪ Y
    R2 = Ri - Y  (keeps X and everything not in Y)
  replace Ri with R1 and R2
return result
```

**Guarantee:** 4NF decomposition is always lossless-join (by Fagin's theorem). However, it may not preserve all dependencies.

---

## Common Exam Patterns

### Factual Questions
- What is the formal definition of an MVD? (for any two tuples with same X, a third tuple exists with swapped Y and Z values)
- State Fagin's theorem. (R losslessly decomposes into R1(X,Y) and R2(X,Z) iff X →→ Y)
- What is the 4NF condition? (every non-trivial MVD X →→ Y requires X to be a superkey)

### Conceptual Questions
- Why can a relation be in BCNF but not 4NF? Give an example.
  (BCNF only considers FD violations; MVDs cause redundancy without any FD violation)
- Explain the symmetry property of MVDs and why it makes sense semantically.
- Why does a FD imply an MVD but not vice versa?

### Solving Questions

**Given a relation and business rules, identify MVDs:**
- "Each employee can have multiple skills and multiple languages; skills and languages are independent"
  → `emp_id →→ skill`, `emp_id →→ language`

**Decompose to 4NF step by step:**
1. Identify the violating MVD (non-trivial, LHS not a superkey)
2. Apply Fagin decomposition: R1 = (X ∪ Y), R2 = R - Y
3. Verify each resulting relation for further 4NF violations
4. Repeat until all are in 4NF

**Check if decomposition is lossless:**
- For binary decomposition R → R1(X,Y), R2(X,Z): lossless iff X →→ Y (or X →→ Z) holds in R
