# SQL Examples

## 1. Create Database
```sql
CREATE DATABASE companydb;
USE companydb;
```

---

## 2. Create User & Assign Privileges (DCL)
```sql
CREATE USER 'hr_user'@'localhost' IDENTIFIED BY 'Secure@123';
GRANT SELECT, INSERT, UPDATE, DELETE ON companydb.* TO 'hr_user'@'localhost';
FLUSH PRIVILEGES;
```

---

## 3. Create Tables (DDL)

### Departments Table
```sql
CREATE TABLE departments (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL
);
```

### Employees Table
```sql
CREATE TABLE employees (
    id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    department_id INT,
    salary DECIMAL(10,2),
    hire_date DATE,
    FOREIGN KEY (department_id) REFERENCES departments(id)
);
```

### Projects Table
```sql
CREATE TABLE projects (
    id INT PRIMARY KEY AUTO_INCREMENT,
    project_name VARCHAR(100) NOT NULL,
    budget DECIMAL(12,2)
);
```

### Employee-Projects Mapping Table
```sql
CREATE TABLE employee_projects (
    employee_id INT,
    project_id INT,
    PRIMARY KEY (employee_id, project_id),
    FOREIGN KEY (employee_id) REFERENCES employees(id),
    FOREIGN KEY (project_id) REFERENCES projects(id)
);
```

---

## 4. Insert Data (DML)
```sql
INSERT INTO departments (name) VALUES ('HR'), ('IT'), ('Finance');

INSERT INTO employees (first_name, last_name, department_id, salary, hire_date)
VALUES ('John', 'Doe', 2, 65000, '2025-11-20');
```

---

## 5. Update Data
```sql
UPDATE employees SET salary = 70000 WHERE id = 1;
```

---

## 6. Delete Data
```sql
DELETE FROM employees WHERE id = 1;
```

---

## 7. Select Queries (DQL)
### Basic Select
```sql
SELECT * FROM employees;
```

### Filter Rows
```sql
SELECT first_name, last_name, salary
FROM employees
WHERE salary > 60000;
```

### Order Results
```sql
SELECT first_name, last_name, salary
FROM employees
ORDER BY salary DESC;
```

---

## 8. Joins
### Inner Join
```sql
SELECT e.first_name, e.last_name, d.name AS department
FROM employees e
INNER JOIN departments d
ON e.department_id = d.id;
```

### Left Join
```sql
SELECT e.first_name, e.last_name, d.name AS department
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.id;
```

---

## 9. Aggregate Functions & GROUP BY
```sql
SELECT d.name AS department,
       COUNT(*) AS emp_count,
       AVG(e.salary) AS avg_salary
FROM employees e
JOIN departments d ON e.department_id = d.id
GROUP BY d.name;
```

---

## 10. Subquery
```sql
SELECT first_name, last_name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

---

## 11. Window Functions (MySQL 8+)
```sql
SELECT first_name, last_name, salary,
       RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;
```

---

## 12. Transaction Control (TCL)
```sql
START TRANSACTION;
UPDATE employees SET salary = salary + 5000 WHERE id = 3;
COMMIT;
```

---

## 13. Drop / Alter / Truncate
### Alter Table
```sql
ALTER TABLE employees ADD email VARCHAR(100);
```

### Truncate Table
```sql
TRUNCATE TABLE employee_projects;
```

### Drop Table
```sql
DROP TABLE projects;
```

