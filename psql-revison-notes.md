# PostgreSQL `psql` Shell Commands — Beginner to Advanced

A practical reference for learning PostgreSQL from zero to advanced. This README organizes useful `psql` meta-commands, SQL statements, administration commands, and command-line utilities by priority. Each section explains what the commands do, when to use them, and important safety notes.

> **Important:** There are three different command categories in this guide:
>
> 1. **`psql` meta-commands** start with a backslash, such as `\l` and `\dt`. Run these inside the `psql` shell.
> 2. **SQL statements** such as `SELECT`, `CREATE TABLE`, and `INSERT`. Run these inside `psql` or another SQL client. Most statements end with `;`.
> 3. **Operating-system commands/utilities** such as `pg_dump` and `pg_restore`. Run these in PowerShell, Command Prompt, or a terminal—not at the `psql` prompt.

## Table of Contents

1. [Connect to PostgreSQL](#1-connect-to-postgresql)
2. [Priority 1: Essential `psql` commands](#2-priority-1-essential-psql-commands)
3. [Priority 2: Database and table basics](#3-priority-2-database-and-table-basics)
4. [Priority 3: CRUD operations](#4-priority-3-crud-operations)
5. [Priority 4: Filtering, sorting, and aggregation](#5-priority-4-filtering-sorting-and-aggregation)
6. [Priority 5: Relationships and joins](#6-priority-5-relationships-and-joins)
7. [Priority 6: Keys, constraints, and data types](#7-priority-6-keys-constraints-and-data-types)
8. [Priority 7: Transactions](#8-priority-7-transactions)
9. [Priority 8: Indexes and performance](#9-priority-8-indexes-and-performance)
10. [Priority 9: SQL files and CSV import/export](#10-priority-9-sql-files-and-csv-importexport)
11. [Priority 10: Roles, permissions, and security](#11-priority-10-roles-permissions-and-security)
12. [Priority 11: Schemas, views, and advanced SQL](#12-priority-11-schemas-views-and-advanced-sql)
13. [Priority 12: Backup and restore](#13-priority-12-backup-and-restore)
14. [Priority 13: Administration and troubleshooting](#14-priority-13-administration-and-troubleshooting)
15. [More useful `psql` commands](#15-more-useful-psql-commands)
16. [Recommended learning order](#16-recommended-learning-order)
17. [Safety checklist](#17-safety-checklist)

---

## 1. Connect to PostgreSQL

Run these commands in **PowerShell**, **Command Prompt**, or another operating-system terminal. They are not SQL statements.

```powershell
# Connect to PostgreSQL using the default user/database settings
psql -U postgres

# Connect to a specific database
psql -U postgres -d school

# Connect to a local PostgreSQL server on port 5432
psql -h localhost -p 5432 -U postgres -d school

# Connect to a remote PostgreSQL database
psql -h your-hostname -p 5432 -U your_username -d your_database
```

**Notes**
- `-h` specifies the server hostname.
- `-p` specifies the port. PostgreSQL commonly uses `5432`.
- `-U` specifies the database role/user.
- `-d` specifies the database.
- Replace example values with your actual details.
- For Neon, copy the connection details from your Neon dashboard. Use the SSL settings provided by Neon; its connection strings commonly include `sslmode=require`.
- Do not put real passwords into a README, public repository, or shell history. Let `psql` prompt for a password when appropriate.

---

## 2. Priority 1: Essential `psql` commands

These are the commands to learn first. Run them **inside the `psql` shell**.

### Help and navigation

```text
\?             Show help for psql meta-commands
\h             Show help for SQL commands
\h SELECT      Show help for SELECT
\l             List databases
\l+            List databases with extra details
\c school      Connect to the database named school
\conninfo      Show information about the current connection
\dt            List tables in the current schema/search path
\dt *.*        List tables across schemas
\d students    Show the structure of the students table
\d+ students   Show detailed information about the students table
\dn            List schemas
\du            List roles/users
\dx            List installed extensions
\q             Exit psql
```

**Notes**
- `\c school` changes the current database connection; replace `school` with your database name.
- `\dt` may show no tables if the current database/schema has no tables.
- `\d students` describes a table, view, or other relation named `students`.
- `\?` is help for `psql` meta-commands; `\h` is help for SQL statements.
- Most meta-commands do **not** need a semicolon.

### Query timing and display

```text
\timing        Toggle query execution-time display
\x             Toggle expanded output
\pset          Show output-format settings/help
\echo Hello    Print text in the psql terminal
\watch 2       Re-run the previous query every 2 seconds
```

**Notes**
- `\timing` is useful for comparing query duration.
- `\x` displays one row as a vertical list of columns, which can help with wide tables.
- `\watch 2` repeatedly executes the previous query; press `Ctrl+C` to stop it.

### Query buffer, history, and editor

```text
\s             Show command history
\e             Open the current query buffer in an external editor
\r             Clear the current query buffer
\p             Print the current query buffer
\g             Execute the current query buffer
\g output.txt  Execute the current query buffer and write results to a file
```

**Notes**
- These are useful when writing or debugging longer queries.
- The editor used by `\e` depends on your environment/editor settings.
- Be careful with `\g output.txt`: it writes query output to a file, so confirm the destination and permissions.

---

## 3. Priority 2: Database and table basics

These are **SQL statements**. Run them inside `psql`; each statement ends with `;`.

### Create and connect to a database

```sql
-- Create a database
CREATE DATABASE school;
```

Then connect to it from `psql`:

```text
\c school
```

**Note:** `CREATE DATABASE` cannot be run inside a transaction block. You need permission to create databases.

### Create a table

```sql
CREATE TABLE students (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    age INTEGER
);
```

**What each part means**
- `CREATE TABLE students`: creates a table named `students`.
- `id INTEGER`: stores an integer identifier.
- `PRIMARY KEY`: uniquely identifies each row and disallows `NULL`.
- `name VARCHAR(100)`: stores text up to 100 characters.
- `NOT NULL`: requires a value for `name`.
- `age INTEGER`: stores an integer age; no age restriction is imposed unless you add a constraint.

### Change or remove table structures

```sql
-- Add a column
ALTER TABLE students ADD COLUMN email VARCHAR(150);

-- Rename a table
ALTER TABLE students RENAME TO learners;

-- Rename the table back to students
ALTER TABLE learners RENAME TO students;

-- Remove a column
ALTER TABLE students DROP COLUMN email;

-- Remove all rows while keeping the table
TRUNCATE TABLE students;

-- Remove the table structure and its data
DROP TABLE students;

-- Remove a database (connect to a different database first)
DROP DATABASE school;
```

**Safety notes**
- `ALTER TABLE` changes the table structure.
- `TRUNCATE` removes all rows but keeps the table. It does not have a `WHERE` clause.
- `DROP TABLE` removes the table itself and its data.
- `DROP DATABASE` removes the database. PostgreSQL does not let you drop the database to which you are currently connected.
- Destructive statements can be difficult or impossible to reverse after commit. Verify the database and table first, and make a backup when needed.

---

## 4. Priority 3: CRUD operations

CRUD means **Create, Read, Update, Delete**.

### Insert data

```sql
INSERT INTO students (id, name, age)
VALUES (1, 'Hasan', 22);

-- Insert multiple rows
INSERT INTO students (id, name, age)
VALUES
    (2, 'Rahim', 20),
    (3, 'Ayesha', 21);

-- Return the inserted row(s)
INSERT INTO students (id, name, age)
VALUES (4, 'Nadia', 23)
RETURNING *;
```

**Notes**
- Text values use single quotes.
- The values must match the columns' data types and constraints.
- `RETURNING *` shows the inserted row(s) immediately.

### Read data

```sql
-- Select every column and row
SELECT * FROM students;

-- Select specific columns
SELECT name, age FROM students;

-- Select only rows matching a condition
SELECT * FROM students WHERE age > 20;
```

**Note:** `SELECT *` is useful for learning and quick inspection. In application code, selecting only the columns you need is often clearer and more efficient.

### Update data

```sql
UPDATE students
SET age = 23
WHERE id = 1
RETURNING *;
```

**Important:** Without a `WHERE` clause, `UPDATE` changes every row in the table.

### Delete data

```sql
DELETE FROM students
WHERE id = 1
RETURNING *;
```

**Important:** Without a `WHERE` clause, `DELETE` removes every row. Check the condition with a `SELECT` first if you are unsure:

```sql
SELECT * FROM students WHERE id = 1;
```

---

## 5. Priority 4: Filtering, sorting, and aggregation

```sql
-- AND
SELECT * FROM students WHERE age >= 18 AND age <= 25;

-- OR
SELECT * FROM students WHERE age = 18 OR age = 20;

-- NOT
SELECT * FROM students WHERE NOT age = 18;

-- IN: match any listed value
SELECT * FROM students WHERE age IN (18, 20, 22);

-- BETWEEN: inclusive range
SELECT * FROM students WHERE age BETWEEN 18 AND 25;

-- LIKE: case-sensitive pattern matching; % means any sequence
SELECT * FROM students WHERE name LIKE 'H%';

-- ILIKE: case-insensitive pattern matching (PostgreSQL-specific)
SELECT * FROM students WHERE name ILIKE 'h%';

-- Check missing values
SELECT * FROM students WHERE age IS NULL;

-- Check values that are present
SELECT * FROM students WHERE age IS NOT NULL;

-- Sort newest/highest first
SELECT * FROM students ORDER BY age DESC;

-- Sort lowest/oldest first
SELECT * FROM students ORDER BY age ASC;

-- Return only 10 rows
SELECT * FROM students LIMIT 10;

-- Skip the first 10 rows (use ORDER BY for predictable pagination)
SELECT * FROM students ORDER BY id LIMIT 10 OFFSET 10;

-- Return unique ages
SELECT DISTINCT age FROM students;

-- Count rows
SELECT COUNT(*) FROM students;

-- Calculate summary values
SELECT AVG(age), MIN(age), MAX(age), SUM(age)
FROM students;

-- Count students in each age group
SELECT age, COUNT(*)
FROM students
GROUP BY age;

-- Show only age groups containing more than one student
SELECT age, COUNT(*)
FROM students
GROUP BY age
HAVING COUNT(*) > 1;
```

**Notes**
- `WHERE` filters rows before grouping; `HAVING` filters groups after `GROUP BY`.
- `COUNT(*)` counts rows. `COUNT(age)` counts rows where `age` is not `NULL`.
- `BETWEEN` includes both endpoints.
- A pattern such as `'H%'` matches names beginning with H. `'%h%'` matches names containing h.
- `NULL` is not the same as zero or an empty string; use `IS NULL`, not `= NULL`.
- Add `ORDER BY` when the result order matters. Without it, row order is not guaranteed.

---

## 6. Priority 5: Relationships and joins

For these examples, imagine you have `customers(id, name)` and `orders(id, customer_id, total)` tables. The `orders.customer_id` column refers to `customers.id`.

```sql
-- INNER JOIN: only matching customer/order pairs
SELECT c.name, o.id, o.total
FROM customers AS c
INNER JOIN orders AS o ON o.customer_id = c.id;

-- LEFT JOIN: all customers, including those without orders
SELECT c.name, o.id, o.total
FROM customers AS c
LEFT JOIN orders AS o ON o.customer_id = c.id;

-- RIGHT JOIN: all orders-side matches and all rows from the right table
SELECT c.name, o.id, o.total
FROM customers AS c
RIGHT JOIN orders AS o ON o.customer_id = c.id;

-- FULL OUTER JOIN: matched and unmatched rows from both sides
SELECT c.name, o.id, o.total
FROM customers AS c
FULL OUTER JOIN orders AS o ON o.customer_id = c.id;

-- CROSS JOIN: every combination of rows
SELECT colors.name, sizes.name
FROM colors CROSS JOIN sizes;

-- UNION: combine compatible result sets and remove duplicates
SELECT name FROM students
UNION
SELECT name FROM teachers;

-- UNION ALL: combine results and keep duplicates
SELECT name FROM students
UNION ALL
SELECT name FROM teachers;

-- Subquery: students older than the average age
SELECT *
FROM students
WHERE age > (SELECT AVG(age) FROM students);
```

**Notes**
- A join combines related rows from multiple tables.
- `INNER JOIN` only returns rows that satisfy the join condition.
- `LEFT JOIN` preserves every row from the left table; missing right-side values appear as `NULL`.
- `RIGHT JOIN` preserves every row from the right table. You can often rewrite it as a `LEFT JOIN` by switching table order.
- `FULL OUTER JOIN` preserves unmatched rows from both sides.
- `UNION` and `UNION ALL` require the same number of compatible columns in each query.
- These examples require the mentioned tables and columns to exist.

---

## 7. Priority 6: Keys, constraints, and data types

### Common constraints

```sql
-- Add a primary key (only if id has no duplicates or NULLs)
ALTER TABLE students ADD PRIMARY KEY (id);

-- Add a unique constraint (email column must exist)
ALTER TABLE students
ADD CONSTRAINT students_email_unique UNIQUE (email);

-- Require every row to have a name
ALTER TABLE students ALTER COLUMN name SET NOT NULL;

-- Require age to be non-negative
ALTER TABLE students
ADD CONSTRAINT students_age_check CHECK (age >= 0);

-- Remove a constraint by name
ALTER TABLE students DROP CONSTRAINT students_age_check;

-- Set a default value
ALTER TABLE students ALTER COLUMN age SET DEFAULT 18;

-- Remove the default
ALTER TABLE students ALTER COLUMN age DROP DEFAULT;
```

### Foreign key

```sql
ALTER TABLE orders
ADD CONSTRAINT orders_customer_fk
FOREIGN KEY (customer_id)
REFERENCES customers(id);
```

**Notes**
- A primary key uniquely identifies each row.
- A foreign key ensures referenced values exist in the referenced table, subject to the constraint.
- A `UNIQUE` constraint prevents duplicate non-`NULL` values under PostgreSQL's default unique semantics.
- `CHECK (age >= 0)` prevents negative ages, but a `CHECK` constraint alone does not reject `NULL`.
- Constraints can fail to be added if existing data violates them.

### Identity columns and practical data types

```sql
CREATE TABLE products (
    id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name TEXT NOT NULL,
    price NUMERIC(10, 2) CHECK (price >= 0),
    is_active BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**Notes**
- `INTEGER` and `BIGINT` store whole numbers.
- `VARCHAR(n)` and `TEXT` store text; `TEXT` is a common choice in PostgreSQL.
- `NUMERIC(10, 2)` stores decimal values with precision suitable for many monetary amounts.
- `BOOLEAN` stores `TRUE` or `FALSE`.
- `DATE` stores a calendar date; `TIMESTAMPTZ` stores a timestamp with time-zone-aware semantics.
- `GENERATED ALWAYS AS IDENTITY` generates IDs automatically. It is usually preferable to manually assigning IDs for typical application tables.
- For financial systems, also consider currency, rounding, and business rules—not only the column type.

---

## 8. Priority 7: Transactions

Transactions group multiple operations into a unit of work.

```sql
-- Start a transaction
BEGIN;

-- Perform related changes
UPDATE accounts SET balance = balance - 500 WHERE id = 1;
UPDATE accounts SET balance = balance + 500 WHERE id = 2;

-- Make the changes permanent
COMMIT;
```

If something goes wrong before commit:

```sql
BEGIN;

UPDATE accounts SET balance = balance - 500 WHERE id = 1;

-- Cancel uncommitted changes
ROLLBACK;
```

### Savepoints and transaction settings

```sql
BEGIN;

SAVEPOINT step_one;

UPDATE students SET age = 24 WHERE id = 1;

-- Undo changes made after this savepoint
ROLLBACK TO SAVEPOINT step_one;

-- Remove the savepoint
RELEASE SAVEPOINT step_one;

COMMIT;
```

```sql
-- Set transaction isolation level (run at the start of a transaction)
BEGIN;
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
-- Run queries here
COMMIT;

-- Make a transaction read-only
BEGIN;
SET TRANSACTION READ ONLY;
SELECT * FROM students;
COMMIT;
```

**Notes**
- `COMMIT` saves transaction changes; `ROLLBACK` cancels uncommitted changes.
- A savepoint allows you to roll back part of a transaction.
- Isolation levels affect how concurrent transactions interact.
- The account-transfer example is illustrative only. A real payment system must check sufficient balance, validate affected row counts, handle concurrent updates, and use appropriate constraints and application logic.

---

## 9. Priority 8: Indexes and performance

```sql
-- Create an index for lookups by name
CREATE INDEX idx_students_name ON students(name);

-- Create a unique index
CREATE UNIQUE INDEX idx_students_email ON students(email);

-- Create a multi-column index
CREATE INDEX idx_students_name_age ON students(name, age);

-- Remove an index
DROP INDEX idx_students_name;

-- Inspect the planned execution strategy
EXPLAIN
SELECT * FROM students WHERE name = 'Hasan';

-- Execute the query and show actual timing/row information
EXPLAIN ANALYZE
SELECT * FROM students WHERE name = 'Hasan';

-- Refresh planner statistics
ANALYZE students;

-- Vacuum and refresh statistics
VACUUM ANALYZE students;
```

**Notes**
- An index can speed up searches, joins, and sorting when it matches the query pattern.
- Indexes consume storage and add overhead to inserts, updates, and deletes. Avoid creating indexes without a reason.
- A multi-column index's usefulness depends on column order and query conditions.
- `EXPLAIN` shows the plan without running the query.
- `EXPLAIN ANALYZE` actually executes the query. Use caution with statements that modify data.
- `ANALYZE` collects statistics used by the query planner.
- `VACUUM` helps reclaim/reuse space from dead tuples and maintain table health; ordinary vacuuming does not usually return all space to the operating system.
- Do not run `VACUUM` inside a transaction block.

---

## 10. Priority 9: SQL files and CSV import/export

### Run a SQL file from inside `psql`

```text
\i setup.sql
\ir setup.sql
```

- `\i setup.sql` executes commands from a file using the specified path.
- `\ir setup.sql` resolves the file path relative to the currently running script.
- Use a full path if the file is not in the current working directory.
- Make sure the file contains SQL or valid `psql` commands.

### Redirect query output

```text
\o output.txt
SELECT * FROM students;
\o
```

- `\o output.txt` sends subsequent query output to a file.
- `\o` with no filename returns output to the terminal.
- Check the path and file permissions before redirecting output.

### Server-side CSV import/export using SQL `COPY`

```sql
-- Export rows to a CSV file on the database server
COPY students TO 'C:/temp/students.csv' WITH (FORMAT CSV, HEADER);

-- Import rows from a CSV file on the database server
COPY students FROM 'C:/temp/students.csv' WITH (FORMAT CSV, HEADER);
```

**Important:** SQL `COPY` reads or writes files from the database server's perspective and requires appropriate server-side file permissions. The example Windows path only works if the PostgreSQL server can access that path.

### Client-side CSV import/export using `\copy`

```text
\copy students TO 'students.csv' WITH (FORMAT CSV, HEADER)
\copy students FROM 'students.csv' WITH (FORMAT CSV, HEADER)
```

- `\copy` reads/writes files from the computer running `psql`.
- It is often easier for local development because it does not require the database server to access the local file directly.
- Ensure the CSV columns, order, encoding, and values match the target table.

### Operating-system command from `psql`

```text
\! cls
```

This asks the operating system to run `cls` (Windows Command Prompt's clear-screen command). It may not work in every terminal or shell environment. The `\!` command executes an external operating-system command, so only run commands you understand.

---

## 11. Priority 10: Roles, permissions, and security

Run these SQL statements with a role that has the required privileges.

```sql
-- Create a login role
CREATE ROLE app_user WITH LOGIN PASSWORD 'replace_with_a_strong_password';

-- Create a role allowed to create databases
CREATE ROLE db_admin WITH LOGIN CREATEDB PASSWORD 'replace_with_a_strong_password';

-- Allow the role to connect to the database
GRANT CONNECT ON DATABASE school TO app_user;

-- Allow the role to use the public schema
GRANT USAGE ON SCHEMA public TO app_user;

-- Grant table-level permissions on existing tables
GRANT SELECT, INSERT, UPDATE, DELETE
ON ALL TABLES IN SCHEMA public TO app_user;

-- Revoke a specific permission
REVOKE DELETE ON TABLE public.students FROM app_user;

-- Change a role's password
ALTER ROLE app_user WITH PASSWORD 'replace_with_a_new_strong_password';

-- Remove a role (may fail while it owns objects or has dependencies)
DROP ROLE app_user;
```

Inside `psql`, related commands include:

```text
\du
\dp
\password
```

**Notes**
- `\du` lists roles; `\dp` displays relation permissions; `\password` provides a safer interactive way to change a password.
- The passwords in these examples are placeholders. Never use them as real passwords.
- `GRANT ... ON ALL TABLES` affects existing tables. For future tables, configure appropriate default privileges with `ALTER DEFAULT PRIVILEGES`.
- Production apps should use a dedicated role with only the permissions they need, not a superuser.
- Granting `CONNECT` and schema `USAGE` does not automatically grant access to every table; table permissions are separate.
- Be careful with `CREATEDB`, `CREATEROLE`, and superuser privileges.

---

## 12. Priority 11: Schemas, views, and advanced SQL

### Schemas

```sql
-- Create a schema
CREATE SCHEMA reporting;

-- Set the schema search path for this session
SET search_path TO reporting, public;

-- Reset to the default search path
RESET search_path;
```

**Note:** A schema groups database objects. `SET search_path` changes how unqualified object names are resolved in the current session; it does not move tables between schemas.

### Views

```sql
-- Create a view
CREATE VIEW public.adult_students AS
SELECT * FROM public.students WHERE age >= 18;

-- Query the view
SELECT * FROM public.adult_students;

-- Remove the view
DROP VIEW public.adult_students;
```

A normal view stores a query definition, not a separate copy of the query's result rows.

### Materialized views

```sql
-- Create a materialized view
CREATE MATERIALIZED VIEW public.student_summary AS
SELECT age, COUNT(*) AS total
FROM public.students
GROUP BY age;

-- Refresh stored results
REFRESH MATERIALIZED VIEW public.student_summary;

-- Remove the materialized view
DROP MATERIALIZED VIEW public.student_summary;
```

A materialized view stores results and can be useful for expensive reports, but the data can become stale until refreshed.

### CTE and window functions

```sql
-- Common Table Expression (CTE)
WITH adult_students AS (
    SELECT * FROM students WHERE age >= 18
)
SELECT * FROM adult_students;

-- Assign a row number based on age
SELECT
    name,
    age,
    ROW_NUMBER() OVER (ORDER BY age DESC) AS row_num
FROM students;
```

**Advanced topics to learn next**
- `CASE`, `COALESCE`, and `NULLIF`
- `EXISTS` and correlated subqueries
- Recursive CTEs (`WITH RECURSIVE`)
- Window functions: `RANK`, `DENSE_RANK`, `LAG`, `LEAD`, running totals
- Functions, procedures, and triggers
- JSON/JSONB and its operators
- PostgreSQL-specific index types such as GIN and GiST

---

## 13. Priority 12: Backup and restore

These are **operating-system terminal commands**, not `psql` meta-commands. Run them in PowerShell, Command Prompt, or a terminal where PostgreSQL utilities are installed and available on `PATH`.

### Custom-format backup and restore

```powershell
# Back up a database in PostgreSQL's custom archive format
pg_dump -U postgres -d school -F c -f school.backup

# Create a separate empty destination database first
createdb -U postgres school_restore

# Restore the archive into the destination database
pg_restore -U postgres -d school_restore school.backup
```

### Plain SQL backup and restore

```powershell
# Save database SQL to a text file
pg_dump -U postgres -d school -f school.sql

# Restore the SQL file into a database
psql -U postgres -d school_restore -f school.sql
```

### Schema-only and data-only backups

```powershell
# Export schema/structure only
pg_dump -U postgres -d school --schema-only -f schema.sql

# Export data only
pg_dump -U postgres -d school --data-only -f data.sql
```

**Notes**
- `pg_dump` backs up one database; `pg_dumpall` can be used for cluster-wide logical dumps, including global objects, with appropriate access.
- `-F c` creates a custom archive, which is typically restored using `pg_restore`.
- Plain SQL dumps are restored with `psql`.
- The target database must exist before restoring a plain SQL dump unless your dump includes suitable database-creation commands.
- The commands may prompt for a password. Avoid putting passwords directly into command arguments or committed scripts.
- Confirm source and destination names before restoring. A restore can overwrite or conflict with existing objects.
- For Neon, use the provider's recommended connection details and ensure the relevant database/user has sufficient privileges.

---

## 14. Priority 13: Administration and troubleshooting

### Identify the current connection and server

```sql
-- Current database and role
SELECT current_database(), current_user;

-- PostgreSQL version and build information
SELECT version();

-- Current session time zone
SHOW TIME ZONE;

-- Set time zone for this session
SET TIME ZONE 'UTC';

-- Reset session-level settings to defaults
RESET ALL;
```

### Inspect active sessions

```sql
SELECT
    pid,
    usename,
    datname,
    state,
    query
FROM pg_stat_activity;
```

**Note:** Visibility into other sessions can depend on your role and privileges. Query text may contain sensitive information, so do not publish raw output without reviewing it.

### Inspect database and table sizes

```sql
-- Database sizes
SELECT
    datname,
    pg_size_pretty(pg_database_size(datname)) AS size
FROM pg_database
ORDER BY pg_database_size(datname) DESC;

-- Total size of a table, including indexes and related storage
SELECT pg_size_pretty(pg_total_relation_size('public.students'));

-- List installed extensions
SELECT * FROM pg_extension;
```

### Extensions

```sql
-- Enable an extension if it is installed and your role is allowed to do so
CREATE EXTENSION IF NOT EXISTS pg_trgm;
```

**Note:** Some extensions require elevated privileges or may not be available on a managed service. Check your provider's supported-extension list first.

---

## 15. More useful `psql` commands

| Command | What it does |
|---|---|
| `\l+` | Lists databases with additional details |
| `\dt+` | Lists tables with additional details |
| `\dv` | Lists views |
| `\dm` | Lists materialized views |
| `\di` | Lists indexes |
| `\ds` | Lists sequences |
| `\df` | Lists functions |
| `\sf function_name` | Shows a function's definition |
| `\dx` | Lists installed extensions |
| `\dp` | Shows table/view/sequence access privileges |
| `\dn+` | Lists schemas with additional details |
| `\du+` | Lists roles with additional details |
| `\conninfo` | Shows the current connection |
| `\password` | Changes a role password interactively |
| `\crosstabview` | Displays the preceding query result as a cross-tab |
| `\! command` | Runs an external operating-system command |
| `\q` | Exits `psql` |

Use `\?` inside `psql` for the full meta-command list and `\h` for SQL syntax help.

---

## 16. Recommended learning order

Do not try to memorize every command at once. Practice in this order:

- [ ] **Stage 1 — Navigation:** `\l`, `\c`, `\dt`, `\d`, `\q`, `CREATE DATABASE`, `CREATE TABLE`
- [ ] **Stage 2 — CRUD:** `INSERT`, `SELECT`, `WHERE`, `UPDATE`, `DELETE`
- [ ] **Stage 3 — Querying:** filtering, sorting, `GROUP BY`, aggregate functions
- [ ] **Stage 4 — Relational design:** primary keys, foreign keys, constraints, joins
- [ ] **Stage 5 — Transactions:** `BEGIN`, `COMMIT`, `ROLLBACK`, savepoints
- [ ] **Stage 6 — Advanced SQL:** subqueries, CTEs, window functions, views
- [ ] **Stage 7 — Performance:** indexes, `EXPLAIN`, `EXPLAIN ANALYZE`, `ANALYZE`
- [ ] **Stage 8 — Operations:** roles, permissions, backup, restore, troubleshooting
- [ ] **Stage 9 — Application integration:** PostgreSQL with Node.js/Next.js, Prisma, and Neon

### Suggested practice project

Build a small student-management database first. Create `students`, `courses`, and `enrollments` tables, then practice inserting records, filtering results, joining tables, adding constraints, and creating indexes. Once comfortable, build an e-commerce schema with `users`, `products`, `orders`, and `order_items`.

---

## 17. Safety checklist

- Check the current database before running destructive commands: `\conninfo` and `SELECT current_database();`.
- Before `UPDATE` or `DELETE`, run a `SELECT` with the same `WHERE` condition.
- Be especially careful with `DROP DATABASE`, `DROP TABLE`, and `TRUNCATE`.
- Use transactions when appropriate, but remember that not every command can run inside a transaction block.
- Keep database passwords and connection strings out of public repositories. Use environment variables and secret management.
- Use a least-privilege database role for applications.
- Back up important data and test restores rather than assuming a backup works.
- Treat production and development databases separately.
- Remember that a local PostgreSQL database and a Neon cloud database are separate databases unless you explicitly migrate or transfer data between them.

---

**Learning tip:** Keep this README as a reference, but learn by running a small set of commands and understanding their output. The goal is not to memorize every command—it is to know which tool to use, what it changes, and how to verify the result.
