# SQL Cheatsheet & Reference Notes

A comprehensive guide to SQL commands, syntax, and operations for structured note-taking.

---

## 1. Data Query Language (DQL)
Used to retrieve data from the database.

```sql
-- Select all columns
SELECT * FROM users;

-- Select specific columns with filtering and sorting
SELECT user_id, username, email 
FROM users 
WHERE status = 'active' AND country = 'MY'
ORDER BY created_at DESC 
LIMIT 10;

-- Aggregate functions and grouping
SELECT 
    department, 
    COUNT(employee_id) AS total_employees, 
    AVG(salary) AS average_salary
FROM employees
WHERE hire_date >= '2025-01-01'
GROUP BY department
HAVING AVG(salary) > 5000;
```

---

## 2. Joins
Used to combine rows from two or more tables based on a related column.

```sql
-- Inner Join (Only matching rows)
SELECT orders.order_id, customers.customer_name
FROM orders
INNER JOIN customers ON orders.customer_id = customers.customer_id;

-- Left Join (All rows from left table, matching rows from right)
SELECT employees.name, departments.department_name
FROM employees
LEFT JOIN departments ON employees.department_id = departments.department_id;
```

---

## 3. Data Manipulation Language (DML)
Used to add, modify, or delete data within tables.

```sql
-- Insert a new record
INSERT INTO users (username, email, status) 
VALUES ('johndoe', 'john@example.com', 'active');

-- Update existing records
UPDATE users 
SET status = 'suspended' 
WHERE last_login < '2026-01-01';

-- Delete records
DELETE FROM users 
WHERE status = 'inactive';
```

---

## 4. Data Definition Language (DDL)
Used to define, alter, or drop database structures (schemas, tables).

```sql
-- Create a new table
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    product_name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2) DEFAULT 0.00,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Modify table structure
ALTER TABLE products ADD COLUMN stock_count INT DEFAULT 0;

-- Delete a table entirely
DROP TABLE old_logs;
```

---

## 5. Data Control Language (DCL) & Transaction Control (TCL)
Used to manage permissions and transactional boundaries.

```sql
-- Grant privileges
GRANT SELECT, INSERT ON database.users TO 'analyst_user'@'localhost';

-- Complete a transaction transaction safely
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
COMMIT; -- Or ROLLBACK; if an error occurs
```