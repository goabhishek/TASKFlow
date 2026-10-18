# PostgreSQL and SQL Basics

## 1. Sample schema
Use this schema for the queries in this handbook.

```sql
CREATE TABLE departments (
    id   BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE employees (
    id            BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name          VARCHAR(100) NOT NULL,
    email         VARCHAR(255) NOT NULL UNIQUE,
    salary        NUMERIC(12, 2) NOT NULL CHECK (salary >= 0),
    department_id BIGINT REFERENCES departments(id),
    manager_id    BIGINT REFERENCES employees(id),
    hired_at      DATE NOT NULL DEFAULT CURRENT_DATE
);

INSERT INTO departments (name)
VALUES ('Engineering'), ('Finance'), ('HR');

INSERT INTO employees (name, email, salary, department_id, hired_at)
VALUES
('Asha', 'asha@example.com', 90000, 1, '2024-01-10'),
('Ravi', 'ravi@example.com', 75000, 1, '2023-06-15'),
('Neha', 'neha@example.com', 82000, 2, '2022-09-01'),
('Kabir', 'kabir@example.com', 60000, NULL, '2025-02-20');
```

## 2. SELECT, WHERE, ORDER BY, LIMIT
```sql
SELECT name, salary
FROM employees
WHERE salary >= 75000
ORDER BY salary DESC
LIMIT 10;
```

## 3. NULL handling
`NULL` represents missing/unknown information. Use `IS NULL` or `IS NOT NULL`, not `= NULL`.

```sql
SELECT name
FROM employees
WHERE department_id IS NULL;
```

Use `COALESCE` to return the first non-null value:
```sql
SELECT name, COALESCE(department_id, 0) AS department_id
FROM employees;
```

## 4. INSERT, UPDATE, DELETE
```sql
INSERT INTO departments (name) VALUES ('Operations');

UPDATE employees
SET salary = salary * 1.05
WHERE department_id = 1;

DELETE FROM employees
WHERE email = 'kabir@example.com';
```
Always verify the `WHERE` clause before running destructive statements. Use a transaction for multi-step changes.

## 5. Constraints
- `PRIMARY KEY`: unique, non-null row identifier.
- `FOREIGN KEY`: referential integrity.
- `UNIQUE`: prevents duplicate values under the constraint's null semantics.
- `NOT NULL`: requires a value.
- `CHECK`: enforces a row-level condition.

## 6. Common questions
**Q: WHERE vs HAVING?** WHERE filters rows before grouping; HAVING filters groups after aggregation.

**Q: DELETE vs TRUNCATE vs DROP?** DELETE removes selected rows; TRUNCATE removes all rows efficiently; DROP removes the table object itself. Transaction and identity behavior should be checked for the exact command and database context.

**Q: What is normalization?** Structuring data to reduce unnecessary duplication and update anomalies while preserving relationships.

## Quick revision
- Use explicit column lists in INSERT.
- Use `IS NULL` for null checks.
- Add constraints to protect data integrity.
- Use parameterized queries in application code.
"""

files["03-PostgreSQL/02-joins-and-subqueries.md"] = r"""
# SQL Joins and Subqueries

## 1. INNER JOIN
Returns rows where join conditions match in both tables.

```sql
SELECT e.name, d.name AS department
FROM employees e
INNER JOIN departments d ON d.id = e.department_id;
```

## 2. LEFT JOIN
Returns every row from the left table and matching rows from the right; unmatched right-side columns are NULL.

```sql
SELECT e.name, d.name AS department
FROM employees e
LEFT JOIN departments d ON d.id = e.department_id;
```

To find employees without a matching department:
```sql
SELECT e.name
FROM employees e
LEFT JOIN departments d ON d.id = e.department_id
WHERE d.id IS NULL;
```

## 3. Self join
A table can be joined to itself, for example to find each employee's manager.

```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON m.id = e.manager_id;
```

## 4. Subquery: above-average salary
```sql
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

## 5. EXISTS
Use EXISTS when the question is whether a matching row exists.

```sql
SELECT d.name
FROM departments d
WHERE EXISTS (
    SELECT 1
    FROM employees e
    WHERE e.department_id = d.id
);
```

## 6. UNION vs UNION ALL
`UNION` combines result sets and removes duplicate rows. `UNION ALL` preserves duplicates and is usually cheaper when deduplication is unnecessary. Both queries must return compatible column counts and types.

## Common questions
- **Can a JOIN multiply rows?** Yes, if the join condition matches multiple rows on the other side.
- **JOIN vs subquery?** Either can express many equivalent queries; choose the clearest form and inspect the execution plan when performance matters.
- **Why can NOT IN behave unexpectedly with NULL?** A NULL in the subquery can make comparisons unknown. `NOT EXISTS` is often safer for anti-joins.
"""

files["03-PostgreSQL/03-aggregations-and-window-functions.md"] = r"""
# Aggregations, CTEs, and Window Functions

## 1. GROUP BY
```sql
SELECT department_id, COUNT(*) AS employee_count, AVG(salary) AS average_salary
FROM employees
GROUP BY department_id
ORDER BY average_salary DESC NULLS LAST;
```

## 2. HAVING
```sql
SELECT department_id, COUNT(*) AS employee_count
FROM employees
WHERE department_id IS NOT NULL
GROUP BY department_id
HAVING COUNT(*) >= 2;
```

## 3. Common Table Expression (CTE)
A CTE names a subquery to make a larger query easier to read.

```sql
WITH department_averages AS (
    SELECT department_id, AVG(salary) AS avg_salary
    FROM employees
    WHERE department_id IS NOT NULL
    GROUP BY department_id
)
SELECT e.name, e.salary, da.avg_salary
FROM employees e
JOIN department_averages da USING (department_id)
WHERE e.salary > da.avg_salary;
```

## 4. ROW_NUMBER, RANK, DENSE_RANK
```sql
SELECT
    name,
    department_id,
    salary,
    ROW_NUMBER() OVER (
        PARTITION BY department_id
        ORDER BY salary DESC
    ) AS row_num,
    RANK() OVER (
        PARTITION BY department_id
        ORDER BY salary DESC
    ) AS salary_rank,
    DENSE_RANK() OVER (
        PARTITION BY department_id
        ORDER BY salary DESC
    ) AS dense_salary_rank
FROM employees;
```
- `ROW_NUMBER`: unique sequential number, even for ties.
- `RANK`: ties share rank and leave gaps.
- `DENSE_RANK`: ties share rank without gaps.

## 5. Highest-paid employee(s) in each department
```sql
WITH ranked AS (
    SELECT e.*,
           DENSE_RANK() OVER (
               PARTITION BY department_id
               ORDER BY salary DESC
           ) AS salary_rank
    FROM employees e
    WHERE department_id IS NOT NULL
)
SELECT name, department_id, salary
FROM ranked
WHERE salary_rank = 1;
```

## Common questions
**Q: GROUP BY vs window function?** GROUP BY collapses rows into groups; window functions calculate across related rows while retaining individual rows.

**Q: What is a CTE?** A named query expression that improves readability and can support recursive queries. Do not assume every CTE is automatically faster than a subquery.

**Q: How do you find the second-highest distinct salary?**
```sql
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
OFFSET 1 LIMIT 1;
```
This returns at most one value; window functions are often clearer when you need associated employee rows or per-department results.
"""

files["03-PostgreSQL/04-indexes-transactions.md"] = r"""
# PostgreSQL Indexes, Transactions, and Query Performance

## 1. Indexes
An index helps PostgreSQL locate rows without scanning the entire table in suitable cases. Indexes consume storage and add overhead to INSERT/UPDATE/DELETE operations.

```sql
CREATE INDEX idx_employees_department_salary
ON employees (department_id, salary DESC);
```

A multicolumn B-tree index is generally most useful when queries constrain its leading columns. Confirm usefulness with actual query plans and workload.

## 2. EXPLAIN and EXPLAIN ANALYZE
```sql
EXPLAIN
SELECT name, salary
FROM employees
WHERE department_id = 1
ORDER BY salary DESC;

EXPLAIN ANALYZE
SELECT name, salary
FROM employees
WHERE department_id = 1
ORDER BY salary DESC;
```
`EXPLAIN ANALYZE` executes the query. Be careful with statements that modify data. Review estimated vs actual rows, scan types, joins, and execution time.

## 3. ACID
- **Atomicity:** transaction changes succeed or fail as a unit.
- **Consistency:** constraints and rules remain satisfied.
- **Isolation:** concurrent transactions are controlled according to the isolation level.
- **Durability:** committed changes persist according to the system's guarantees.

## 4. Transaction example
```sql
BEGIN;

UPDATE employees
SET salary = salary - 1000
WHERE id = 1;

UPDATE employees
SET salary = salary + 1000
WHERE id = 2;

COMMIT;
-- Use ROLLBACK instead of COMMIT if validation fails.
```

## 5. Isolation levels
PostgreSQL supports Read Committed (default), Repeatable Read, and Serializable (among its SQL isolation levels; it also accepts Read Uncommitted but treats it as Read Committed). Higher isolation can require retrying transactions after serialization failures.

## 6. Normalization and denormalization
Normalization reduces duplication and update anomalies. Denormalization intentionally duplicates or precomputes data to improve read patterns, with additional consistency complexity.

## 7. Common questions
**Q: Does every query benefit from an index?** No. Small tables, low-selectivity filters, or queries that read much of a table may use sequential scans more efficiently.

**Q: What is a composite index?** An index on multiple columns; column order matters for many query patterns.

**Q: What causes slow SQL?** Possible causes include poor query plans, missing or unsuitable indexes, inaccurate statistics, unnecessary rows/columns, inefficient joins, lock waits, and I/O constraints. Diagnose with plans and measurements.

**Q: Why use transactions?** To make related database operations behave as a logical unit and manage consistency under failures/concurrency.
"""

files["03-PostgreSQL/05-advanced-sql-interview-questions.md"] = r"""
# PostgreSQL Interview Questions

1. **Primary key vs unique constraint?** A primary key identifies each row and is unique and non-null; a table has one primary key, though it can contain multiple columns. Multiple unique constraints are possible.
2. **WHERE vs HAVING?** Filter rows before grouping vs filter grouped results.
3. **INNER JOIN vs LEFT JOIN?** Only matching rows vs all left-side rows plus matches.
4. **UNION vs UNION ALL?** Deduplicates vs preserves duplicates.
5. **What is an index?** A data structure that can accelerate suitable lookups at storage and write-maintenance cost.
6. **What is a composite index?** An index covering multiple columns; leading-column order matters.
7. **What is a transaction?** A group of operations committed or rolled back as a unit.
8. **Explain ACID.** Atomicity, consistency, isolation, durability.
9. **What is a deadlock?** Transactions wait on locks held by one another; PostgreSQL detects deadlocks and aborts a transaction.
10. **What is a CTE?** A named query expression using `WITH`.
11. **What is a window function?** A calculation over related rows that preserves row-level output.
12. **RANK vs DENSE_RANK?** Both assign equal ranks to ties; RANK leaves gaps, DENSE_RANK does not.
13. **How to find duplicates?**
```sql
SELECT email, COUNT(*)
FROM employees
GROUP BY email
HAVING COUNT(*) > 1;
```
14. **How to find the nth-highest salary?** Use `DENSE_RANK()` ordered by salary descending, then filter for the desired rank.
15. **What is normalization?** Organizing tables and relationships to reduce redundancy and anomalies.
16. **How to troubleshoot a slow query?** Capture the query, inspect `EXPLAIN (ANALYZE, BUFFERS)` where safe, inspect statistics and indexes, and measure changes.
17. **What is a foreign key?** A constraint ensuring referenced values exist in the referenced table, subject to its null and action rules.
18. **What does NULL mean?** Unknown or missing; comparisons can yield UNKNOWN, so use `IS NULL`.
19. **DELETE vs TRUNCATE?** DELETE can filter rows; TRUNCATE removes all rows and has different locking/trigger/identity behavior.
20. **Why parameterized queries?** They separate data from SQL structure and help prevent SQL injection.
"""

