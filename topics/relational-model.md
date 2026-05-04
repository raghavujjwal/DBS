---
layout: default
title: Relational Model & Query Languages
---

# Relational Model & Query Languages

---

## The Relational Model

Proposed by **E.F. Codd (1970)**, the relational model organizes data as a collection of **relations** (tables). It provides:
- A simple, uniform representation of all data
- A mathematical foundation (set theory, first-order predicate logic)
- Data independence: physical storage can change without affecting logical queries

### Key Terminology

| Formal Term | Common Term | Meaning |
|-------------|-------------|---------|
| Relation | Table | A named, 2-dimensional structure |
| Tuple | Row / Record | A single data entry |
| Attribute | Column / Field | A named property |
| Domain | Data type | Set of permitted values for an attribute |
| Degree | Arity | Number of attributes in a relation |
| Cardinality | — | Number of tuples in a relation |

### Properties of a Relation
1. Each relation has a unique name
2. Each attribute has a unique name within the relation
3. Attribute values are atomic (1NF requirement)
4. No duplicate tuples (relations are sets, not bags)
5. Tuple ordering is irrelevant
6. Attribute ordering within a tuple is irrelevant (but consistent with schema)

---

## Keys

### Types of Keys

| Key Type | Definition |
|----------|-----------|
| **Superkey** | Any attribute set that uniquely identifies tuples |
| **Candidate key** | Minimal superkey (no proper subset is also a superkey) |
| **Primary key (PK)** | Chosen candidate key used to identify tuples |
| **Foreign key (FK)** | Attribute(s) in one relation referencing the PK of another |
| **Prime attribute** | Attribute that is part of some candidate key |
| **Non-prime attribute** | Attribute that is not part of any candidate key |

### Integrity Constraints

**Entity Integrity:** Primary key attributes must NOT be NULL. Every tuple must have a valid, non-null identifier.

**Referential Integrity:** If a foreign key value exists in a referencing table, the referenced value must exist in the referenced table (or the FK must be NULL).

**Referential Integrity Violation Handling:**
- `RESTRICT` (default): reject the operation
- `CASCADE`: propagate the change (delete/update) to referencing rows
- `SET NULL`: set FK to NULL in referencing rows
- `SET DEFAULT`: set FK to a default value

---

## NULL Values

NULL represents **unknown** or **not applicable** — it is NOT zero, empty string, or "N/A".

### Three-Valued Logic (3VL)

When NULLs are involved in comparisons, the result can be TRUE, FALSE, or **UNKNOWN**.

**AND table:**

| AND | T | F | U |
|-----|---|---|---|
| **T** | T | F | U |
| **F** | F | F | F |
| **U** | U | F | U |

**OR table:**

| OR | T | F | U |
|----|---|---|---|
| **T** | T | T | T |
| **F** | T | F | U |
| **U** | T | U | U |

**NOT:**
- NOT TRUE = FALSE
- NOT FALSE = TRUE
- NOT UNKNOWN = UNKNOWN

**Critical rule:** A WHERE clause only passes tuples where the condition evaluates to **TRUE** — tuples where condition is UNKNOWN are excluded.

**Comparison with NULL:**
- `salary = NULL` → UNKNOWN (not TRUE or FALSE!)
- `NULL = NULL` → UNKNOWN
- Use `IS NULL` or `IS NOT NULL` for proper NULL checks

### Aggregation and NULLs
- `COUNT(*)` counts all rows including NULLs
- `COUNT(attribute)` counts only non-NULL values
- `SUM`, `AVG`, `MIN`, `MAX` all ignore NULLs
- `GROUP BY` treats NULLs as equal (NULLs group together)

---

## Relational Algebra (RA)

Relational Algebra is a **procedural** query language — you specify HOW to retrieve data step by step. It operates on relations and returns relations (closure property).

### Fundamental Operations

#### 1. Selection (σ) — Horizontal filter
Selects tuples that satisfy a condition.
```
σ_condition(R)
```
- Returns a subset of **rows**
- Condition can use: =, ≠, <, >, ≤, ≥, AND, OR, NOT
- Example: `σ_(salary > 50000)(EMPLOYEE)` — all employees earning more than 50K

#### 2. Projection (π) — Vertical filter
Selects specific attributes (columns).
```
π_attr1, attr2, ...(R)
```
- Returns a subset of **columns**
- Duplicates are eliminated (set semantics)
- Example: `π_(name, salary)(EMPLOYEE)`

#### 3. Rename (ρ)
Renames a relation or its attributes.
```
ρ_S(R)          — renames relation R to S
ρ_S(A1, A2)(R)  — renames relation and attributes
```

#### 4. Union (∪)
Returns all tuples in R or S (or both).
```
R ∪ S
```
- R and S must be **union-compatible**: same degree, compatible domains
- Duplicates eliminated

#### 5. Set Difference (−)
Returns tuples in R that are NOT in S.
```
R − S
```
- R and S must be union-compatible

#### 6. Cartesian Product (×)
Combines every tuple of R with every tuple of S.
```
R × S
```
- If R has m tuples and n attributes, S has p tuples and q attributes → result has m×p tuples and n+q attributes
- Usually combined with selection to form a join

### Derived Operations

#### Natural Join (⋈)
Joins relations on all common attributes, keeping only matching tuples.
```
R ⋈ S
```
Equivalent to: `π_combined_attrs(σ_R.A=S.A(R × S))` for shared attribute A

#### Theta Join (⋈_θ)
Join with a general condition θ.
```
R ⋈_θ S = σ_θ(R × S)
```

#### Equi-join
Theta join where θ uses only equality conditions.

#### Division (÷)
Returns tuples in R that match ALL tuples in S.
```
R ÷ S
```
- R has attributes (A, B), S has attribute (B)
- Result has attribute A
- Returns A values where ALL B values in S appear in R
- Used for "find all X that did Y for ALL Z" queries

**Example:** Find students who have taken ALL courses offered:
`ENROLLMENT(student_id, course_id) ÷ COURSE(course_id)`

#### Intersection (∩)
Returns tuples present in both R and S.
```
R ∩ S = R − (R − S)
```

### Outer Joins

| Type | Tuples Kept |
|------|-------------|
| Left Outer Join (⟕) | All from left + matching from right; unmatched right → NULLs |
| Right Outer Join (⟖) | All from right + matching from left; unmatched left → NULLs |
| Full Outer Join (⟗) | All from both; unmatched padded with NULLs |

---

## Relational Calculus (RC)

RC is a **declarative** query language — you specify WHAT you want, not how to compute it.

### Tuple Relational Calculus (TRC)

```
{t | P(t)}
```
Returns all tuples t that satisfy predicate P.

**Example:** Find all employees with salary > 50000:
```
{t | t ∈ EMPLOYEE ∧ t.salary > 50000}
```

**Example:** Find names of employees who work in dept D5:
```
{t.name | t ∈ EMPLOYEE ∧ t.dept_id = 'D5'}
```

### Quantifiers in TRC

**Existential (∃):** "`there exists some t such that...`"
```
{t.name | t ∈ EMPLOYEE ∧ ∃d (d ∈ DEPARTMENT ∧ d.dept_id = t.dept_id ∧ d.location = 'Houston')}
```

**Universal (∀):** "`for all t...`"
Used for division-like queries ("for every").

### Domain Relational Calculus (DRC)
Variables range over attribute values (domains) rather than tuples.
```
{<x1, x2, ...> | P(x1, x2, ...)}
```
Forms the basis for QBE (Query-by-Example).

### Safety of Expressions
An RC expression is **safe** if it is guaranteed to produce a finite result. Unsafe expressions reference infinite domains (e.g., "all tuples NOT in R"). The relational algebra is inherently safe.

### Codd's Theorem
Any query expressible in Relational Algebra can be expressed in both TRC and DRC, and vice versa. They are **relationally complete** — equivalent in expressive power.

---

## Bag Semantics (SQL vs RA)

Pure relational algebra uses **set semantics** (no duplicates). SQL uses **bag semantics** (duplicates allowed unless explicitly eliminated).

| Operation | Set (RA) | Bag (SQL) |
|-----------|----------|-----------|
| SELECT | σ (deduplicates) | SELECT (keeps duplicates) |
| Distinct | implicit | `SELECT DISTINCT` |
| UNION | eliminates duplicates | `UNION ALL` keeps duplicates; `UNION` eliminates |
| Intersection | eliminates duplicates | `INTERSECT` |
| Difference | eliminates duplicates | `EXCEPT` |

---

## Common Exam Patterns

### Factual Questions
- State the entity integrity rule. (PK cannot be NULL)
- What does the division operator return? (tuples in R that match ALL in S)
- What is union-compatibility? (same degree, compatible attribute domains)

### Conceptual Questions
- Explain three-valued logic and how NULL affects WHERE clause filtering.
- Why does relational algebra have the "closure" property?
- Compare TRC and DRC — what is the variable range in each?
- What is the difference between an equi-join and a natural join?

### Solving Questions

**RA expression writing:** Given a schema, write RA expressions for:
- "Find names of employees in department D5 earning more than 50000"
  `π_name(σ_(dept_id='D5' ∧ salary>50000)(EMPLOYEE))`
- "Find names of employees who work on ALL projects in Houston"
  Uses division: `(π_(emp_id, proj_id)(WORKS_ON)) ÷ (π_proj_id(σ_(location='Houston')(PROJECT)))`
  then join back to get names.

**3VL truth table:** Evaluate compound conditions involving NULLs step by step.
