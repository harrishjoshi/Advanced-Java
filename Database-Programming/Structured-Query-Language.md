# Table of Contents

- [Structured Query Language (SQL)](#structured-query-language-sql)
  - [What is SQL?](#what-is-sql)
  - [Categories of SQL Commands](#categories-of-sql-commands)
  - [DDL Commands](#ddl-commands)
  - [DML Commands](#dml-commands)
  - [DQL (Data Query Language) - SELECT](#dql-data-query-language---select)
  - [TCL Commands](#tcl-commands)
  - [DCL Commands](#dcl-commands)
  - [Common Clauses](#common-clauses)
  - [Joins](#joins)
  - [Subqueries](#subqueries)
  - [Aggregate Functions & GROUP BY](#aggregate-functions--group-by)
  - [Window Functions (Modern Addition)](#window-functions)

---

## **Structured Query Language (SQL)**

### **What is SQL?**
**SQL (Structured Query Language)** is a standardized, declarative programming language used for managing, manipulating, and querying data in a relational database management system (RDBMS).  
It allows users to define database structure, insert/update/delete data, control access, and retrieve data efficiently.

SQL is an **ANSI/ISO standard** and is supported (with minor dialect variations) by all major RDBMS: MySQL, PostgreSQL, Oracle, SQL Server, SQLite, MariaDB, etc.

### **Categories of SQL Commands**

| Category | Full Form                       | Purpose                                      | Main Commands                  |
|----------|---------------------------------|----------------------------------------------|--------------------------------|
| DDL      | Data Definition Language        | Define and manage database structure         | CREATE, ALTER, DROP, TRUNCATE  |
| DML      | Data Manipulation Language      | Manipulate data                              | INSERT, UPDATE, DELETE, MERGE  |
| DQL      | Data Query Language             | Retrieve data                                | SELECT                         |
| TCL      | Transaction Control Language    | Manage transactions                          | COMMIT, ROLLBACK, SAVEPOINT    |
| DCL      | Data Control Language           | Manage permissions and access control        | GRANT, REVOKE                  |

> **Note**: Strictly per ANSI/ISO, only DDL, DML, TCL, and DCL are official categories.  
> DQL (SELECT only) is a widely used teaching convention but not part of the official standard.

### **DDL Commands** (Data Definition Language)

| Command       | Description                                      | Common Example |
|---------------|--------------------------------------------------|----------------|
| `CREATE`      | Create database, table, index, view, etc.        | `CREATE TABLE employees (...);` |
| `ALTER`       | Modify existing objects                          | `ALTER TABLE employees ADD COLUMN salary DECIMAL(10,2);` |
| `DROP`        | Permanently delete objects                       | `DROP TABLE employees;` |
| `TRUNCATE`    | Remove all rows (keeps structure, faster than DELETE) | `TRUNCATE TABLE employees;` |
| `RENAME`      | Rename objects (syntax varies by DBMS)           | MySQL: `RENAME TABLE old TO new;`<br>PostgreSQL: `ALTER TABLE old RENAME TO new;` |

### **DML Commands** (Data Manipulation Language)

| Command       | Description                                      | Example |
|---------------|--------------------------------------------------|---------|
| `INSERT`      | Add new rows                                     | `INSERT INTO employees (id, name, salary) VALUES (1, 'John', 50000);` |
| `UPDATE`      | Modify existing rows                             | `UPDATE employees SET salary = 60000 WHERE id = 1;` |
| `DELETE`      | Remove rows                                      | `DELETE FROM employees WHERE id = 1;` |
| `MERGE` / `UPSERT` | Insert or update (depending on DBMS)        | Supported natively in Oracle, SQL Server, PostgreSQL 9.5+, etc.

### **DQL (Data Query Language) - SELECT**

```sql
SELECT [DISTINCT] column_list | *
FROM table_name
[WHERE condition]
[GROUP BY column(s)]
[HAVING condition]
[ORDER BY column(s) [ASC|DESC]]
[LIMIT n] [OFFSET m];   -- MySQL, PostgreSQL, SQLite
-- or: FETCH FIRST n ROWS ONLY / OFFSET m ROWS  (Oracle 12c+, PostgreSQL, SQL Server 2012+)
-- or: SELECT TOP n ... (SQL Server older syntax)
```

### **TCL Commands** (Transaction Control)

| Command       | Description                                      |
|---------------|--------------------------------------------------|
| `COMMIT`      | Permanently save changes                         |
| `ROLLBACK`    | Undo changes since last COMMIT                   |
| `SAVEPOINT`   | Create a point for partial rollback              |
| `SET TRANSACTION` | Define isolation level, read-only, etc.     |

### **DCL Commands** (Data Control Language)

| Command | Description | Example |
|---------|-----------|---------|
| `GRANT` | Grant privileges to users/roles | `GRANT SELECT, INSERT ON employees TO 'user1'@'localhost';` |
| `REVOKE`| Remove privileges               | `REVOKE DELETE ON employees FROM 'user1'@'localhost';` |

Common privileges: `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `CREATE`, `ALTER`, `DROP`, `EXECUTE`, `ALL PRIVILEGES`

### **Common Clauses**

| Clause        | Purpose                                          | Example |
|---------------|--------------------------------------------------|---------|
| `WHERE`       | Filter rows before grouping                      | `WHERE salary > 50000` |
| `ORDER BY`    | Sort results                                     | `ORDER BY salary DESC, name ASC` |
| `LIMIT / OFFSET` | Pagination                                   | `LIMIT 10 OFFSET 20` |
| `LIKE` / `ILIKE` | Pattern matching (ILIKE = case-insensitive in PostgreSQL) | `WHERE name LIKE 'A%'` |
| `IN`          | Match any value in list                          | `WHERE dept IN ('IT', 'HR')` |
| `BETWEEN`     | Inclusive range                                  | `WHERE salary BETWEEN 40000 AND 80000` |

### **Joins**

| Join Type         | Description                                          | Keeps |
|-------------------|------------------------------------------------------|-------|
| `INNER JOIN`      | Only rows with matches in both tables                | Matching rows |
| `LEFT JOIN`       | All rows from left table + matching from right       | All left |
| `RIGHT JOIN`      | All rows from right table + matching from left       | All right |
| `FULL OUTER JOIN` | All rows from both tables (with NULLs where no match)| All from both |
| `CROSS JOIN`      | Cartesian product                                    | Every combination |

> MySQL still lacks native `FULL OUTER JOIN` (as of 8.0/8.4 in 2025) → simulate with `LEFT JOIN ... UNION ... RIGHT JOIN`

### **Subqueries**

- Scalar (returns single value)
- Row or column subqueries
- Correlated subqueries
- Can appear in `SELECT`, `FROM`, `WHERE`, `HAVING`

```sql
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

### **Aggregate Functions & GROUP BY**

| Function      | Description                  |
|---------------|------------------------------|
| `COUNT()`     | Count rows                   |
| `SUM()`       | Total                        |
| `AVG()`       | Average                      |
| `MIN()` / `MAX()` | Minimum / Maximum        |

```sql
SELECT department,
       COUNT(*) AS emp_count,
       AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING COUNT(*) > 5
ORDER BY avg_salary DESC;
```

### **Window Functions** (Highly recommended in modern SQL)

```sql
SELECT name, salary, department,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rank_in_dept,
       AVG(salary) OVER (PARTITION BY department) AS dept_avg_salary
FROM employees;
```

Common window functions: `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `NTILE()`, `LAG()`, `LEAD()`, `SUM() OVER()`, etc.

### **Key Points to Remember**
- SQL keywords are case-insensitive; identifiers may be case-sensitive depending on DBMS and quoting.
- Always terminate statements with `;`
- Use single quotes for string literals (`'John'`)
- `NULL` means unknown/missing — not equal to anything, including itself
- Primary Key = Unique + NOT NULL
- Foreign Key enforces referential integrity
- Use `COALESCE()`, `NULLIF()`, `CASE` for handling NULLs and logic

