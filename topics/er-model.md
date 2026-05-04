---
layout: default
title: ER Model & Mapping
---

# ER Model & Mapping to Relations

---

## What is the ER Model?

The **Entity-Relationship (ER) Model** is a high-level conceptual data model used to describe data requirements during database design. It captures the structure of data in terms of entities, their attributes, and relationships among entities — independent of any particular DBMS.

> **Key idea:** Design at the conceptual level first (what data exists, how it relates), then map to the logical level (relational schema).

---

## Entities and Entity Sets

An **entity** is a real-world "thing" that can be distinctly identified (e.g., a specific student, a specific course).

An **entity set** is a collection of similar entities sharing the same structure (e.g., all Students, all Courses).

### Strong vs Weak Entities

| | Strong Entity | Weak Entity |
|--|--------------|-------------|
| Has its own key? | Yes | No — identified by owner's key + partial key |
| Existence dependent? | Independent | Depends on owner entity |
| Notation | Rectangle | Double rectangle |
| Relationship to owner | — | Identifying relationship (double diamond) |

**Example:** `ORDER_ITEM` is a weak entity — it cannot be uniquely identified without knowing which `ORDER` it belongs to. The `line_number` alone is not unique across all orders, but `(order_id, line_number)` is.

### Partial Key (Discriminator)
The attribute(s) that, combined with the owner's key, uniquely identify a weak entity instance. Shown with a dashed underline in ER diagrams.

---

## Attributes

### Types of Attributes

| Type | Description | Example |
|------|-------------|---------|
| **Simple** | Atomic, cannot be subdivided | `age`, `gender` |
| **Composite** | Can be divided into sub-parts | `name` → `{first_name, last_name}` |
| **Single-valued** | One value per entity | `SSN` |
| **Multi-valued** | Multiple values per entity | `phone_numbers`, `degrees_held` |
| **Derived** | Computed from other attributes | `age` derived from `birth_date` |
| **Null** | Attribute doesn't apply or is unknown | optional phone number |

### Composite Attributes
`address` → `{street_address, city, state, zip_code}`
`street_address` → `{house_number, street_name}`

In a relational mapping, composite attributes are flattened — only the leaf-level simple attributes are stored.

### Multi-valued Attributes
Stored in a **separate relation** with a foreign key to the owning entity. Example: `EMPLOYEE_PHONE(emp_id, phone_number)`.

### Derived Attributes
Typically **not stored** — computed on the fly. If stored for performance, a trigger must maintain consistency.

---

## Relationships and Relationship Sets

A **relationship** is an association among two or more entities.
A **relationship set** is a set of relationships of the same type.

### Degree of a Relationship
- **Binary:** between 2 entity sets (most common)
- **Ternary:** among 3 entity sets
- **n-ary:** among n entity sets

### Relationship Attributes
Relationships can have their own attributes. Example: `WORKS_FOR(employee, department)` might have `start_date` as a relationship attribute.

In M:N relationships, these attributes become columns in the junction table. In 1:N relationships, they can be placed in the N-side entity table.

---

## Cardinality Constraints

Cardinality defines how many entity instances on one side can relate to how many on the other.

| Type | Meaning | Example |
|------|---------|---------|
| **1:1** | One entity associates with at most one other | Person — Passport |
| **1:N** | One entity associates with many | Department — Employee |
| **M:N** | Many entities on both sides | Student — Course |

### Participation Constraints

| Type | Meaning | Notation |
|------|---------|----------|
| **Total (mandatory)** | Every entity MUST participate | Double line |
| **Partial (optional)** | Entity may or may not participate | Single line |

**Example:** Every `EMPLOYEE` must work for a `DEPARTMENT` (total participation of EMPLOYEE). But a `DEPARTMENT` may exist without employees initially (partial participation of DEPARTMENT).

### (min, max) Notation
More precise than crow's foot. `(min, max)` on the relationship line next to the entity:
- `(1,1)`: must participate in exactly 1 relationship instance
- `(0,1)`: optional, at most 1
- `(0,N)`: optional, unlimited
- `(1,N)`: must participate in at least 1

---

## Specialization, Generalization, and Inheritance

### Generalization (bottom-up)
Multiple entity sets are combined into a higher-level superclass. Example: `CAR` and `TRUCK` are generalized into `VEHICLE`.

### Specialization (top-down)
A higher-level entity is divided into sub-entities based on distinguishing characteristics.

### Inheritance
Subclasses inherit all attributes and relationships of the superclass.

### Constraints on Specialization

**Disjointness:**
- **Disjoint:** An entity can belong to at most one subclass (`d` in ER notation)
- **Overlapping:** An entity can belong to multiple subclasses (`o`)

**Completeness:**
- **Total:** Every superclass entity must belong to at least one subclass
- **Partial:** A superclass entity may not belong to any subclass

---

## Aggregation

Used when a **relationship participates in another relationship**. A relationship set is treated as a higher-level entity set.

**Example:** `EMPLOYEE` works on `PROJECT` (the `WORKS_ON` relationship). A `MANAGER` manages this employee-project combination. Instead of a ternary relationship, represent `WORKS_ON` as an aggregation and have `MANAGES` relate `MANAGER` to the aggregated `WORKS_ON`.

---

## Mapping ER to Relational Schema

### Rule 1: Strong Entity Sets
- Create a table with all simple attributes
- Composite attributes: include only the leaf simple attributes
- Multi-valued attributes: separate table (see Rule 6)
- Derived attributes: do not include
- **PK** = entity's key attribute(s)

### Rule 2: Weak Entity Sets
- Create a table with all simple attributes of the weak entity
- Add the primary key of the owner entity as a **foreign key**
- **PK** = (partial key of weak entity) + (PK of owner entity)
- **FK** = owner's PK, with ON DELETE CASCADE

**Example:**
```
ORDER(order_id, order_date, customer_id)
ORDER_ITEM(order_id, line_number, quantity, product_id)
  PK: (order_id, line_number)
  FK: order_id → ORDER
```

### Rule 3: Binary 1:1 Relationships
Three options — choose based on participation:

| Participation | Preferred Strategy |
|--------------|-------------------|
| Total on both sides | Merge into a single table |
| Total on one side (say E1) | Add FK + relationship attributes into E1's table (E1 has total participation, so FK will never be NULL) |
| Partial on both sides | FK in either table; choose the side more likely to participate |

**Why prefer total-participation side?** Placing the FK on the partial side creates NULLs for non-participating entities — wasted space and complicates queries.

### Rule 4: Binary 1:N Relationships
- Add the primary key of the **1-side** entity as a **foreign key** in the **N-side** entity's table
- Move any relationship attributes to the N-side table

**Example:**
```
DEPARTMENT(dept_id, dept_name, location)
EMPLOYEE(emp_id, name, salary, dept_id)  ← FK dept_id → DEPARTMENT
```

### Rule 5: Binary M:N Relationships
- Create a new **junction (cross-reference) table**
- Table contains: PK of entity set A + PK of entity set B + any relationship attributes
- **PK** of new table = combination of both entity PKs
- **FK** constraints to both original tables

**Example:**
```
STUDENT(student_id, name)
COURSE(course_id, title)
ENROLLMENT(student_id, course_id, grade, semester)
  PK: (student_id, course_id)
  FK: student_id → STUDENT, course_id → COURSE
```

### Rule 6: Multi-Valued Attributes
- Create a separate table: entity's PK + the multi-valued attribute
- **PK** = (entity PK + multi-valued attribute value)
- This ensures no multi-valued attribute in the original entity table

**Example:**
```
EMPLOYEE(emp_id, name)
EMPLOYEE_PHONE(emp_id, phone_number)
  PK: (emp_id, phone_number)
  FK: emp_id → EMPLOYEE
```

### Rule 7: N-ary Relationships (Ternary+)
- Create a new table with PKs of all participating entities + relationship attributes
- PK = combination of all participant PKs (unless cardinality constraints reduce this)

### Rule 8: Specialization/Generalization
Three approaches:

**Option A — Single table with type flag (for total, overlapping):**
- One table for the superclass with all attributes of all subclasses
- NULLs for inapplicable attributes
- Add a `type` discriminator column
- Pro: simple, efficient for queries spanning all types
- Con: many NULLs, can violate normalization

**Option B — Table per subclass (for disjoint specialization):**
- No table for superclass
- Each subclass table includes superclass attributes + own attributes
- Con: queries across all entities need UNION

**Option C — Superclass table + subclass tables (most common):**
- One table for superclass with inherited attributes
- Each subclass table has only its own additional attributes + FK to superclass
- Best for queries on superclass; subclass queries require JOIN

---

## Common Exam Patterns

### Factual Questions
- What notation represents a weak entity? (double rectangle, double diamond for identifying relationship)
- What is a partial key? (dashed underline)
- When does an M:N relationship require a new table? (always)

### Conceptual Questions
- Why should derived attributes not be stored in the ER-to-relational mapping?
- Explain why we prefer to put the FK on the total-participation side in a 1:1 mapping.
- What is the difference between a disjoint and overlapping specialization?

### Solving Questions
Given an ER diagram, write the relational schema with all PKs, FKs, and constraints. Common traps:
- Weak entities: include owner PK in both PK and FK
- M:N: always create a new table, never embed
- 1:1 total-partial: FK goes in the total side
- Multi-valued attributes: separate table required
