---
layout: default
title: Normalization & Functional Dependencies
---

# Normalization & Functional Dependencies

---

## Why Normalize?

A poorly designed schema leads to **update anomalies**:

| Anomaly | Description | Example |
|---------|-------------|---------|
| **Insertion** | Can't add data without unrelated data | Can't add a department without an employee |
| **Deletion** | Deleting data loses unrelated data | Deleting last employee also deletes department info |
| **Modification** | Updating one fact requires many changes | Employee's department name appears in many rows |

The goal of normalization is to eliminate these anomalies by decomposing relations into smaller, well-structured ones.

---

## Functional Dependencies (FDs)

A **functional dependency (FD)** X → Y holds in relation R if, for any two tuples t1 and t2 in R, whenever t1[X] = t2[X], then t1[Y] = t2[Y].

> "X determines Y" — knowing the value of X uniquely determines the value of Y.

**Example:** In EMPLOYEE(emp_id, name, dept_id, dept_name):
- `emp_id → name` (knowing emp_id tells us the name)
- `dept_id → dept_name` (knowing dept_id tells us dept_name)
- `emp_id → dept_id` (emp works in one dept)
- `emp_id → dept_name` (transitively)

### Trivial vs Non-trivial FDs
- **Trivial:** Y ⊆ X — e.g., `{A, B} → A` (always holds, vacuously true)
- **Non-trivial:** Y ⊄ X — these are the ones that matter for normalization

### Full vs Partial FDs
Given a composite key {A, B} → C:
- **Full FD:** C is dependent on {A, B} AND NOT on A alone or B alone
- **Partial FD:** C depends on a proper subset of the key — a violation of 2NF

---

## Armstrong's Axioms

A complete and sound set of inference rules for FDs:

| Rule | Statement | Note |
|------|-----------|------|
| **Reflexivity** | If Y ⊆ X, then X → Y | Generates trivial FDs |
| **Augmentation** | If X → Y, then XZ → YZ | Add same attrs to both sides |
| **Transitivity** | If X → Y and Y → Z, then X → Z | Chain rule |

### Derived Rules (from the three axioms)
| Rule | Statement |
|------|-----------|
| **Union** | If X → Y and X → Z, then X → YZ |
| **Decomposition** | If X → YZ, then X → Y and X → Z |
| **Pseudo-transitivity** | If X → Y and WY → Z, then WX → Z |

---

## Attribute Closure Algorithm

**Purpose:** Determine what attributes are functionally determined by a set X, given a set F of FDs.

```
Algorithm: Closure(X, F)
  X⁺ = X
  repeat:
    for each FD (Y → Z) in F:
      if Y ⊆ X⁺:
        X⁺ = X⁺ ∪ Z
  until X⁺ does not change
  return X⁺
```

**Use cases:**
1. Check if X is a superkey: is X⁺ = all attributes?
2. Check if FD X → Y holds: is Y ⊆ X⁺?
3. Find candidate keys
4. Compute canonical cover

**Worked example:**
Schema: R(A, B, C, D, E), FDs: {A→BC, CD→E, B→D, E→A}

Find A⁺:
- Start: A⁺ = {A}
- A → BC: A⁺ = {A, B, C}
- B → D: A⁺ = {A, B, C, D}
- CD → E (CD ⊆ {A,B,C,D}? Yes): A⁺ = {A, B, C, D, E}
- E → A (already have A)
- Result: A⁺ = {A, B, C, D, E} = all attributes → A is a superkey (and candidate key if no proper subset is also a superkey)

---

## Finding Candidate Keys

**Algorithm:**
1. Find attributes that appear **only on the LHS** of FDs (never determined) — these must be in every candidate key
2. Find attributes that appear **in no FD** — these must also be in every candidate key
3. Compute the closure of the set from steps 1 and 2
4. If closure = all attributes → this is the unique candidate key
5. Otherwise, try adding attributes from those that appear on both LHS and RHS of FDs

**Example:**
R(A, B, C, D), FDs: {AB→C, C→D, D→A}
- B appears only on LHS (in AB→C) — must be in every CK
- Compute {B}⁺: start with {B}, no FD has only B on LHS → stuck at {B}
- Add A: {AB}⁺ = {AB} → (AB→C) → {ABC} → (C→D) → {ABCD} ✓ CK: AB
- Try without A: need B + something → add C: {BC}⁺ = {BC} → (C→D) → {BCD} → (D→A) → {ABCD} ✓ CK: BC
- Try BD: {BD}⁺ = {BD} → (D→A) → {ABD} → (AB→C) → {ABCD} ✓ CK: BD
- Candidate keys: AB, BC, BD

---

## Normal Forms

### 1NF (First Normal Form)
**Definition:** All attribute values are **atomic** — no multi-valued or composite attributes.

Every relation in the relational model is technically in 1NF by definition (attributes are atomic domains). 1NF is violated when:
- An attribute stores a list or set of values
- An attribute is a composite (can be decomposed)

**Fix:** Flatten composite attributes, create separate tables for multi-valued attributes.

### 2NF (Second Normal Form)
**Definition:** In 1NF AND every non-prime attribute is **fully functionally dependent** on every candidate key (no partial dependencies).

**Violation:** A non-prime attribute depends on only part of a composite candidate key.

**Example violation:**
```
ENROLLMENT(student_id, course_id, grade, course_name)
FDs: (student_id, course_id) → grade
     course_id → course_name  ← partial dependency!
```
`course_name` depends on just `course_id`, not the full key.

**Fix:** Decompose to remove partial dependency:
```
ENROLLMENT(student_id, course_id, grade)
COURSE(course_id, course_name)
```

**Note:** If a relation has a single-attribute candidate key, it's automatically in 2NF (no partial dependencies possible).

### 3NF (Third Normal Form)
**Definition:** In 2NF AND no non-prime attribute is **transitively dependent** on any candidate key.

**Violation:** A non-prime attribute depends on another non-prime attribute (which depends on the key).

**Example violation:**
```
EMPLOYEE(emp_id, dept_id, dept_name)
FDs: emp_id → dept_id
     dept_id → dept_name  ← dept_name transitively depends on emp_id!
```

**Fix:** Decompose:
```
EMPLOYEE(emp_id, dept_id)
DEPARTMENT(dept_id, dept_name)
```

**3NF Formal Definition (for exam):**
For every non-trivial FD X → A in R:
- X is a superkey of R, **OR**
- A is a prime attribute (part of some candidate key)

### BCNF (Boyce-Codd Normal Form)
**Definition:** For every non-trivial FD X → Y in R, **X must be a superkey**.

BCNF is stricter than 3NF — it removes the "A is prime" exception.

**Example:** A relation can be in 3NF but not BCNF when:
```
TEACHING(student, course, teacher)
FDs: (student, course) → teacher  ← (s,c) is a CK
     teacher → course  ← teacher is not a superkey, but course is prime!
```
This is in 3NF (course is prime attribute) but NOT in BCNF (teacher → course violates BCNF).

### BCNF Decomposition Algorithm
```
Given relation R and FDs F:
result = {R}
while some relation Ri in result is not in BCNF:
  find a non-trivial FD X → Y in Ri that violates BCNF (X is not superkey)
  decompose Ri into:
    R1 = X ∪ Y  (X → Y, so X is key of R1)
    R2 = Ri − Y  (contains all other attributes + X)
  replace Ri with R1 and R2 in result
return result
```

**Guarantee:** BCNF decomposition always produces lossless-join decomposition. But it may NOT preserve all FDs.

**Trade-off:** BCNF vs 3NF:
- BCNF: eliminates all anomalies, may lose dependency preservation
- 3NF: always achieves both lossless join AND dependency preservation, but allows some redundancy

---

## Lossless Join Decomposition

A decomposition of R into R1 and R2 is **lossless** iff:
```
R1 ∩ R2 → R1    OR    R1 ∩ R2 → R2
```
i.e., the common attributes form a superkey of at least one of the sub-relations.

**Intuition:** If the common attributes uniquely identify tuples in one of the relations, we can always reconstruct R exactly by joining R1 and R2.

**Lossy decomposition:** The natural join of R1 and R2 produces **spurious tuples** — fake rows that weren't in the original relation.

---

## Dependency Preservation

A decomposition {R1, R2, ..., Rk} of R preserves dependencies iff:
```
(F1 ∪ F2 ∪ ... ∪ Fk)⁺ = F⁺
```
where Fi is the projection of F onto Ri (FDs that only involve attributes of Ri).

**Why it matters:** If a FD X → Y is not preserved in any sub-relation, enforcing it requires joining multiple relations — expensive!

**Checking:** For each original FD X → Y:
1. Compute X⁺ using only the FDs from the decomposed relations
2. If Y ⊆ X⁺ → dependency is preserved

---

## Canonical (Minimal) Cover

A **canonical cover** Fc of F is a minimal set of FDs equivalent to F (same closure). Used in 3NF synthesis.

**Algorithm:**
1. **Decompose RHS:** Replace each FD X → {A, B, C} with X → A, X → B, X → C
2. **Remove extraneous LHS attributes:** For each FD X → A where |X| > 1:
   - For each attribute B in X, test if (X - B) → A can be derived from remaining FDs
   - If yes (i.e., A ∈ (X-B)⁺ with respect to F), remove B from X's LHS
3. **Remove redundant FDs:** For each FD X → A, check if A ∈ X⁺ computed from (F - {X→A})
   - If yes, the FD is redundant — remove it

**Result:** Fc is equivalent to F but has no redundant attributes on LHS, no redundant FDs, and single attributes on RHS.

---

## 3NF Synthesis Algorithm

Guarantees both lossless join AND dependency preservation.

```
1. Compute canonical cover Fc of F
2. For each FD X → A in Fc:
   create relation schema R_i = X ∪ A  with FD X → A
3. If no schema in result contains a candidate key of R:
   add a schema containing a candidate key of R (ensures lossless join)
4. Remove any schema that is a subset of another schema
```

**Key property:** 3NF synthesis always produces a decomposition that is:
- In 3NF
- Lossless join
- Dependency preserving

---

## Common Exam Patterns

### Factual Questions
- State the 2NF definition. (No partial dependency of non-prime attribute on any CK)
- What is an extraneous attribute? (Attribute that can be removed from FD LHS without changing closure)
- State BCNF condition. (For every non-trivial X → Y, X is a superkey)

### Conceptual Questions
- Why can't BCNF decomposition always preserve dependencies?
- A relation is in BCNF. Is it necessarily in 3NF? (Yes — BCNF ⊂ 3NF)
- Can a relation with only prime attributes have anomalies? (Yes — normalization doesn't fully protect relations with all-prime attributes)

### Solving Questions

**Full normalization walkthrough** (most common exam type):
1. Given a schema R and a set of FDs F:
   - Find all candidate keys using closure
   - Identify prime and non-prime attributes
   - Check each NF in order (1NF → 2NF → 3NF → BCNF)
   - Decompose to achieve target NF
   - Verify lossless join after each decomposition step
   - Check dependency preservation

**Canonical cover computation:**
- Step-by-step application of all three simplification rules
- Verify each removal by checking closures

**Lossless join test after decomposition:**
- Compute R1 ∩ R2
- Check if this common set functionally determines all attributes of R1 or R2
