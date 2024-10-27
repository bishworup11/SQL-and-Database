    
# SQL 
  
## Index
   - [Introduction to SQL](#introduction-to-sql)
   - [SQL Commands](#sql-commands)
   - [Execution Order](#execution-order)
   - [Sample Table](#sample-table)
   - [SELECT](#select)
   - [WHERE](#where)
   - [ORDER BY (ASC | DESC)](#order-by-asc--desc)
   - [INSERT INTO](#insert-into)
   - [NULL VALUE](#null-value)
   - [UPDATE Syntax](#update-syntax)
   - [DELETE Syntax](#delete-syntax)
   - [LIMIT Syntax](#limit-syntax)
   - [MIN(), MAX()](#min-max)
   - [COUNT(), AVG(), SUM()](#count-avg-sum)
   - [Wildcard Characters](#wildcard-characters)
   - [LIKE Syntax](#like-syntax)
   - [IN Syntax](#in-syntax)
   - [BETWEEN Syntax](#between-syntax)
   - [Alias (AS) Column Syntax](#alias-as-column-syntax)
   - [Joining Tables](#joining-tables)
   - [UNION](#union)
   - [GROUP BY](#group-by)
   - [HAVING](#having)
   - [EXISTS](#exists)
   - [ANY and ALL](#any-and-all)
   - [INSERT INTO (with SELECT)](#insert-into-with-select)
   - [CASE Syntax](#case-syntax)
   - [IFNULL() and COALESCE()](#ifnull-and-coalesce)
   - [Section Two](#section-two)
   - [Create Database](#create-database)
   - [Drop Database](#drop-database)
   - [CREATE TABLE](#create-table)
   - [Drop Table](#drop-table)
   - [Alter Table](#alter-table)
   - [Create Constraints](#create-constraints)
   - [Create Index](#create-index)
   - [AUTO_INCREMENT](#auto_increment)
   - [Date Data Types](#date-data-types)
   - [Create View](#create-view)
   - [One-to-many Relationship](#one-to-many-relationship)
   - [Many-to-Many Relationship](#many-to-many-relationship)


### Introduction to SQL
[Index](#index)
 

**SQL** stands for **Structured Query Language**.
- **Purpose**: Lets you access and manipulate databases.

**RDBMS** stands for **Relational Database Management System**.
- **Data Storage**: Data is stored in database objects called **tables**.
- **Relationships**: A relational database defines database relationships in the form of tables.

### SQL Commands
[Index](#index)
 

1. **SELECT** - Extracts data from a database.
2. **UPDATE** - Updates data in a database.
3. **DELETE** - Deletes data from a database.
4. **INSERT INTO** - Inserts new data into a database.
5. **CREATE DATABASE** - Creates a new database.
6. **ALTER DATABASE** - Modifies a database.
7. **CREATE TABLE** - Creates a new table.
8. **ALTER TABLE** - Modifies a table.
9. **DROP TABLE** - Deletes a table.
10. **CREATE INDEX** - Creates an index (search key).
11. **DROP INDEX** - Deletes an index.


### Execution Order:

[Index](#index)

```sql
SELECT DISTINCT
    column1,
    column2,
    ROW_NUMBER() OVER (PARTITION BY column2 ORDER BY column1) AS row_num
FROM table1
JOIN table2 ON table1.id = table2.id
WHERE column1 > 10
GROUP BY column2
HAVING COUNT(column2) > 5
ORDER BY column1
LIMIT 10 OFFSET 5;
```

1. **FROM clause**: `table1` and `table2` are accessed, and the join condition `table1.id = table2.id` is applied.
2. **WHERE clause**: Rows are filtered where `column1 > 10`.
3. **GROUP BY clause**: Rows are grouped by `column2`.
4. **Aggregation function**: An aggregation function, such as `SUM(column1)`, is applied to each group of rows based on `column2`.
5. **HAVING clause**: Groups are filtered where `COUNT(column2) > 5`.
6. **Window Functions**: The window function `ROW_NUMBER() OVER (PARTITION BY column2 ORDER BY column1)` is applied to assign a unique row number to each row within each partition of `column2`, ordered by `column1`.
7. **SELECT clause**: `column1`, `column2`, and the window function result `row_num` are selected.
8. **DISTINCT clause**: Duplicate rows are removed.
9. **ORDER BY clause**: The result set is sorted by `column1`.
10. **LIMIT clause**: Only the first 10 rows are returned.
11. **OFFSET clause**: The result set starts from the 6th row (since `OFFSET` is 5, it skips the first 5 rows).

Understanding this order helps in optimizing queries and ensuring that the desired results are obtained efficiently.

---

This format includes the `OFFSET` clause and a window function, providing a comprehensive overview of the execution order for a `SELECT` statement in MySQL.

### Sample Table
[Index](#index)
 

Let's create a sample table called `Employees`:

```sql
CREATE TABLE Employees (
    ID INT PRIMARY KEY,
    Name VARCHAR(100),
    Age INT,
    Department VARCHAR(50),
    Salary DECIMAL(10, 2)
);
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|

### SELECT
[Index](#index)
 

**Creating an example table**

```sql
INSERT INTO Employees (ID, Name, Age, Department, Salary) VALUES
(1, 'John Doe', 30, 'HR', 50000.00),
(2, 'Jane Smith', 25, 'IT', 60000.00),
(3, 'Mike Johnson', 35, 'HR', 55000.00),
(4, 'Emily Davis', 28, 'IT', 65000.00);
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|

**Full table retrieval**

```sql
SELECT * FROM Employees;
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|

**Selecting specific columns**

```sql
SELECT Name, Department FROM Employees;
```

**Physical View of Result:**

| Name          | Department |
|---------------|------------|
| John Doe      | HR         |
| Jane Smith    | IT         |
| Mike Johnson  | HR         |
| Emily Davis   | IT         |

**Using DISTINCT to remove duplicates**

```sql
SELECT DISTINCT Department FROM Employees;
```

**Physical View of Result:**

| Department |
|------------|
| HR         |
| IT         |

**Counting distinct rows**

```sql
SELECT COUNT(DISTINCT Department) FROM Employees;
```

**Physical View of Result:**

| COUNT(DISTINCT Department) |
|----------------------------|
| 2                          |

**Counting distinct rows with multiple columns**

```sql
SELECT COUNT(DISTINCT Department, Age) FROM Employees;
```

**Physical View of Result:**

| COUNT(DISTINCT Department, Age) |
|--------------------------------|
| 4                              |

### WHERE
[Index](#index)
 

**Filtering rows based on conditions**

```sql
SELECT * FROM Employees WHERE Age > 30;
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|

**Using BETWEEN**

```sql
SELECT * FROM Employees WHERE Age BETWEEN 25 AND 30;
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|

**Using LIKE for pattern matching**

```sql
SELECT * FROM Employees WHERE Name LIKE 'J%';
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|

**Using IN for specific values**

```sql
SELECT * FROM Employees WHERE Department IN ('HR', 'IT');
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|

**Combining conditions with AND, OR, NOT**

```sql
SELECT * FROM Employees WHERE Age > 30 AND Department = 'HR';
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|

### ORDER BY (ASC | DESC)
[Index](#index)
 

**Sorting results in ascending or descending order**

```sql
SELECT * FROM Employees ORDER BY Age ASC;
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|

**Sorting by multiple columns**

```sql
SELECT * FROM Employees ORDER BY Department ASC, Age DESC;
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|

### INSERT INTO
[Index](#index)
 

**Adding values to all columns**

```sql
INSERT INTO Employees (ID, Name, Age, Department, Salary) VALUES
(5, 'David Brown', 29, 'Finance', 70000.00);
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 5   | David Brown   | 29  | Finance    | 70000.00|

**Adding values to specific columns**

```sql
INSERT INTO Employees (ID, Name, Department) VALUES
(6, 'Sarah Wilson', 'Marketing');
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 5   | David Brown   | 29  | Finance    | 70000.00|
| 6   | Sarah Wilson  | NULL| Marketing  | NULL    |

### NULL VALUE
[Index](#index)
 

**Handling NULL values**

```sql
SELECT * FROM Employees WHERE Salary IS NULL;
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 6   | Sarah Wilson  | NULL| Marketing  | NULL    |

**Selecting rows with NULL values**

```sql
SELECT * FROM Employees WHERE Salary IS NOT NULL;
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 5   | David Brown   | 29  | Finance    | 70000.00|

### UPDATE Syntax
[Index](#index)
 

**Updating specific rows**

```sql
UPDATE Employees SET Salary = 75000.00 WHERE ID = 5;
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 5   | David Brown   | 29  | Finance    | 75000.00|
| 6   | Sarah Wilson  | NULL| Marketing  | NULL    |

**Updating all rows**

```sql
UPDATE Employees SET Department = 'General';
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | General    | 50000.00|
| 2   | Jane Smith    | 25  | General    | 60000.00|
| 3   | Mike Johnson  | 35  | General    | 55000.00|
| 4   | Emily Davis   | 28  | General    | 65000.00|
| 5   | David Brown   | 29  | General    | 75000.00|
| 6   | Sarah Wilson  | NULL| General    | NULL    |

### DELETE Syntax
[Index](#index)
 

**Deleting specific rows**

```sql
DELETE FROM Employees WHERE ID = 6;
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | General    | 50000.00|
| 2   | Jane Smith    | 25  | General    | 60000.00|
| 3   | Mike Johnson  | 35  | General    | 55000.00|
| 4   | Emily Davis   | 28  | General    | 65000.00|
| 5   | David Brown   | 29  | General    | 75000.00|

**Deleting all rows**

```sql
DELETE FROM Employees;
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|

### LIMIT Syntax
[Index](#index)
 

**Limiting the number of rows returned**

```sql
INSERT INTO Employees (ID, Name, Age, Department, Salary) VALUES
(1, 'John Doe', 30, 'HR', 50000.00),
(2, 'Jane Smith', 25, 'IT', 60000.00),
(3, 'Mike Johnson', 35, 'HR', 55000.00),
(4, 'Emily Davis', 28, 'IT', 65000.00),
(5, 'David Brown', 29, 'Finance', 70000.00);
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 5   | David Brown   | 29  | Finance    | 70000.00|

```sql
SELECT * FROM Employees LIMIT 3;
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|

**Using OFFSET for pagination**

```sql
SELECT * FROM Employees LIMIT 2 OFFSET 2;
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|

### MIN(), MAX()
[Index](#index)
 

**Finding the minimum and maximum values**

```sql
SELECT MIN(Age), MAX(Age) FROM Employees;
```

**Physical View of Result:**

| MIN(Age) | MAX(Age) |
|----------|----------|
| 25       | 35       |

### COUNT(), AVG(), SUM()
[Index](#index)
 

**Counting rows**

```sql
SELECT COUNT(*) FROM Employees;
```

**Physical View of Result:**

| COUNT(*) |
|----------|
| 5        |

**Calculating the average**

```sql
SELECT AVG(Salary) FROM Employees;
```

**Physical View of Result:**

| AVG(Salary) |
|-------------|
| 60000.00    |

**Summing values**

```sql
SELECT SUM(Salary) FROM Employees;
```

**Physical View of Result:**

| SUM(Salary) |
|-------------|
| 300000.00   |

### Wildcard Characters
[Index](#index)
 

**Using % and _ for pattern matching**

```sql
SELECT * FROM Employees WHERE Name LIKE 'J%';
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|

### LIKE Syntax
[Index](#index)
 

**Using LIKE with wildcards**

```sql
SELECT * FROM Employees WHERE Name LIKE 'M%';
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|

### IN Syntax
[Index](#index)
 

**Using IN with specific values**

```sql
SELECT * FROM Employees WHERE Department IN ('HR', 'IT');
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|

**Using IN with a subquery**

```sql
SELECT * FROM Employees WHERE Department IN (SELECT Department FROM Departments);
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|

### BETWEEN Syntax
[Index](#index)
 

**Filtering rows within a range**

```sql
SELECT * FROM Employees WHERE Age BETWEEN 25 AND 30;
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|

### Alias (AS) Column Syntax
[Index](#index)
 

**Renaming columns in the result set**

```sql
SELECT Name AS EmployeeName, Department AS Dept FROM Employees;
```

**Physical View of Result:**

| EmployeeName | Dept   |
|--------------|--------|
| John Doe     | HR     |
| Jane Smith   | IT     |
| Mike Johnson | HR     |
| Emily Davis  | IT     |
| David Brown  | Finance|

### Joining Tables
[Index](#index)
 

**INNER JOIN**

```sql
SELECT Employees.Name, Departments.DepartmentName
FROM Employees
INNER JOIN Departments ON Employees.Department = Departments.DepartmentID;
```

**Physical View of Result:**

| Name          | DepartmentName |
|---------------|----------------|
| John Doe      | HR             |
| Jane Smith    | IT             |
| Mike Johnson  | HR             |
| Emily Davis   | IT             |
| David Brown   | Finance        |

**LEFT JOIN**

```sql
SELECT Employees.Name, Departments.DepartmentName
FROM Employees
LEFT JOIN Departments ON Employees.Department = Departments.DepartmentID;
```

**Physical View of Result:**

| Name          | DepartmentName |
|---------------|----------------|
| John Doe      | HR             |
| Jane Smith    | IT             |
| Mike Johnson  | HR             |
| Emily Davis   | IT             |
| David Brown   | Finance        |

**RIGHT JOIN**

```sql
SELECT Employees.Name, Departments.DepartmentName
FROM Employees
RIGHT JOIN Departments ON Employees.Department = Departments.DepartmentID;
```

**Physical View of Result:**

| Name          | DepartmentName |
|---------------|----------------|
| John Doe      | HR             |
| Jane Smith    | IT             |
| Mike Johnson  | HR             |
| Emily Davis   | IT             |
| David Brown   | Finance        |

**CROSS JOIN**

```sql
SELECT Employees.Name, Departments.DepartmentName
FROM Employees
CROSS JOIN Departments;
```

**Physical View of Result:**

| Name          | DepartmentName |
|---------------|----------------|
| John Doe      | HR             |
| John Doe      | IT             |
| John Doe      | Finance        |
| Jane Smith    | HR             |
| Jane Smith    | IT             |
| Jane Smith    | Finance        |
| Mike Johnson  | HR             |
| Mike Johnson  | IT             |
| Mike Johnson  | Finance        |
| Emily Davis   | HR             |
| Emily Davis   | IT             |
| Emily Davis   | Finance        |
| David Brown   | HR             |
| David Brown   | IT             |
| David Brown   | Finance        |

**Self Join**

**Joining a table with itself**

```sql
SELECT a.Name AS Employee1, b.Name AS Employee2
FROM Employees a, Employees b
WHERE a.ID < b.ID;
```

**Physical View of Result:**

| Employee1    | Employee2     |
|--------------|---------------|
| John Doe     | Jane Smith    |
| John Doe     | Mike Johnson  |
| John Doe     | Emily Davis   |
| John Doe     | David Brown   |
| Jane Smith   | Mike Johnson  |
| Jane Smith   | Emily Davis   |
| Jane Smith   | David Brown   |
| Mike Johnson | Emily Davis   |
| Mike Johnson | David Brown   |
| Emily Davis  | David Brown   |

### UNION
[Index](#index)
 

**Combining results from multiple SELECT statements**

```sql
SELECT Name FROM Employees
UNION
SELECT Name FROM Managers;
```

**Physical View of Result:**

| Name          |
|---------------|
| John Doe      |
| Jane Smith    |
| Mike Johnson  |
| Emily Davis   |
| David Brown   |
| Manager1      |
| Manager2      |

### GROUP BY
[Index](#index)
 

**Grouping rows by a column**

```sql
SELECT Department, COUNT(*) FROM Employees
GROUP BY Department;
```

**Physical View of Result:**

| Department | COUNT(*) |
|------------|----------|
| HR         | 2        |
| IT         | 2        |
| Finance    | 1        |

**Grouping and ordering results**

```sql
SELECT Department, COUNT(*) FROM Employees
GROUP BY Department
ORDER BY COUNT(*) DESC;
```

**Physical View of Result:**

| Department | COUNT(*) |
|------------|----------|
| HR         | 2        |
| IT         | 2        |
| Finance    | 1        |

### HAVING
[Index](#index)
 

**Filtering groups based on conditions**

```sql
SELECT Department, COUNT(*) FROM Employees
GROUP BY Department
HAVING COUNT(*) > 1;
```

**Physical View of Result:**

| Department | COUNT(*) |
|------------|----------|
| HR         | 2        |
| IT         | 2        |

### EXISTS
[Index](#index)
 

**Checking for the existence of records in a subquery**

```sql
SELECT * FROM Employees
WHERE EXISTS (SELECT * FROM Departments WHERE Employees.Department = Departments.DepartmentID);
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 5   | David Brown   | 29  | Finance    | 70000.00|

### ANY and ALL
[Index](#index)
 

**ANY Operator**

```sql
SELECT * FROM Employees
WHERE Salary > ANY (SELECT Salary FROM Managers);
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 5   | David Brown   | 29  | Finance    | 70000.00|

**ALL Operator**

```sql
SELECT * FROM Employees
WHERE Salary > ALL (SELECT Salary FROM Managers);
```

**Physical View of Result:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 5   | David Brown   | 29  | Finance    | 70000.00|

### INSERT INTO (with SELECT)
[Index](#index)
 

**Inserting data from another table**

```sql
INSERT INTO Employees (ID, Name, Age, Department, Salary)
SELECT ID, Name, Age, Department, Salary FROM TempEmployees;
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 5   | David Brown   | 29  | Finance    | 70000.00|
| 6   | Temp Employee | 32  | HR         | 52000.00|

### CASE Syntax
[Index](#index)
 

**Using CASE for conditional logic**

```sql
SELECT Name,
       CASE
           WHEN Age > 30 THEN 'Senior'
           ELSE 'Junior'
       END AS AgeGroup
FROM Employees;
```

**Physical View of Result:**

| Name          | AgeGroup |
|---------------|----------|
| John Doe      | Junior   |
| Jane Smith    | Junior   |
| Mike Johnson  | Senior   |
| Emily Davis   | Junior   |
| David Brown   | Junior   |
| Temp Employee | Senior   |

### IFNULL() and COALESCE()
[Index](#index)
 

**Handling NULL values with IFNULL()**

```sql
SELECT Name, IFNULL(Salary, 0) AS Salary
FROM Employees;
```

**Physical View of Result:**

| Name          | Salary  |
|---------------|---------|
| John Doe      | 50000.00|
| Jane Smith    | 60000.00|
| Mike Johnson  | 55000.00|
| Emily Davis   | 65000.00|
| David Brown   | 70000.00|
| Temp Employee | 52000.00|

**Handling NULL values with COALESCE()**

```sql
SELECT Name, COALESCE(Salary, 0) AS Salary
FROM Employees;
```

**Physical View of Result:**

| Name          | Salary  |
|---------------|---------|
| John Doe      | 50000.00|
| Jane Smith    | 60000.00|
| Mike Johnson  | 55000.00|
| Emily Davis   | 65000.00|
| David Brown   | 70000.00|
| Temp Employee | 52000.00|

### Section Two
[Index](#index)
 

### Create Database
[Index](#index)
 

**Creating a new database**

```sql
CREATE DATABASE CompanyDB;
```

### Drop Database
[Index](#index)
 

**Deleting a database**

```sql
DROP DATABASE CompanyDB;
```

### CREATE TABLE
[Index](#index)
 

**Creating a new table**

```sql
CREATE TABLE Employees (
    ID INT PRIMARY KEY,
    Name VARCHAR(100),
    Age INT,
    Department VARCHAR(50),
    Salary DECIMAL(10, 2)
);
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|

**Creating a table using another table**

```sql
CREATE TABLE NewEmployees AS SELECT * FROM Employees;
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 5   | David Brown   | 29  | Finance    | 70000.00|
| 6   | Temp Employee | 32  | HR         | 52000.00|

### Drop Table
[Index](#index)
 

**Deleting a table**

```sql
DROP TABLE Employees;
```

### Alter Table
[Index](#index)
 

**Adding a column**

```sql
ALTER TABLE Employees ADD COLUMN Email VARCHAR(100);
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  | Email         |
|-----|---------------|-----|------------|---------|---------------|
| 1   | John Doe      | 30  | HR         | 50000.00| NULL          |
| 2   | Jane Smith    | 25  | IT         | 60000.00| NULL          |
| 3   | Mike Johnson  | 35  | HR         | 55000.00| NULL          |
| 4   | Emily Davis   | 28  | IT         | 65000.00| NULL          |
| 5   | David Brown   | 29  | Finance    | 70000.00| NULL          |
| 6   | Temp Employee | 32  | HR         | 52000.00| NULL          |

**Dropping a column**

```sql
ALTER TABLE Employees DROP COLUMN Email;
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 5   | David Brown   | 29  | Finance    | 70000.00|
| 6   | Temp Employee | 32  | HR         | 52000.00|

**Modifying a column**

```sql
ALTER TABLE Employees MODIFY COLUMN Age INT NOT NULL;
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 5   | David Brown   | 29  | Finance    | 70000.00|
| 6   | Temp Employee | 32  | HR         | 52000.00|

### Create Constraints
[Index](#index)
 

**NOT NULL**

```sql
ALTER TABLE Employees MODIFY COLUMN Name VARCHAR(100) NOT NULL;
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|
| 1   | John Doe      | 30  | HR         | 50000.00|
| 2   | Jane Smith    | 25  | IT         | 60000.00|
| 3   | Mike Johnson  | 35  | HR         | 55000.00|
| 4   | Emily Davis   | 28  | IT         | 65000.00|
| 5   | David Brown   | 29  | Finance    | 70000.00|
| 6   | Temp Employee | 32  | HR         | 52000.00|

**UNIQUE Constraint**

```sql
ALTER TABLE Employees ADD CONSTRAINT UniqueEmail UNIQUE (Email);
```

**PRIMARY KEY**

```sql
ALTER TABLE Employees ADD PRIMARY KEY (ID);
```

**FOREIGN KEY**

```sql
ALTER TABLE Employees ADD CONSTRAINT FK_Department FOREIGN KEY (Department) REFERENCES Departments(DepartmentID);
```

**CHECK Constraint**

```sql
ALTER TABLE Employees ADD CONSTRAINT CheckAge CHECK (Age >= 18);
```

**DEFAULT**

```sql
ALTER TABLE Employees MODIFY COLUMN Age INT DEFAULT 25;
```

### Create Index
[Index](#index)
 

**Creating an index**

```sql
CREATE INDEX idx_Name ON Employees (Name);
```

**Creating a unique index**

```sql
CREATE UNIQUE INDEX idx_Email ON Employees (Email);
```

**Dropping an index**

```sql
DROP INDEX idx_Name ON Employees;
```

### AUTO_INCREMENT
[Index](#index)
 

**Using AUTO_INCREMENT for automatic numbering**

```sql
CREATE TABLE Employees (
    ID INT AUTO_INCREMENT PRIMARY KEY,
    Name VARCHAR(100),
    Age INT,
    Department VARCHAR(50),
    Salary DECIMAL(10, 2)
);
```

**Physical View of Table:**

| ID  | Name          | Age | Department | Salary  |
|-----|---------------|-----|------------|---------|

### Date Data Types
[Index](#index)
 

**Overview of date data types**

- DATE: Stores date values (e.g., '2023-10-01')
- TIME: Stores time values (e.g., '14:30:00')
- DATETIME: Stores both date and time values (e.g., '2023-10-01 14:30:00')
- TIMESTAMP: Stores date and time values with timezone information (e.g., '2023-10-01 14:30:00')

### Create View
[Index](#index)
 

**Creating a view**

```sql
CREATE VIEW EmployeeView AS
SELECT Name, Department FROM Employees;
```

**Physical View of View:**

| Name          | Department |
|---------------|------------|
| John Doe      | HR         |
| Jane Smith    | IT         |
| Mike Johnson  | HR         |
| Emily Davis   | IT         |
| David Brown   | Finance    |
| Temp Employee | HR         |

**Replacing a view**

```sql
CREATE OR REPLACE VIEW EmployeeView AS
SELECT Name, Department, Salary FROM Employees;
```

**Physical View of View:**

| Name          | Department | Salary  |
|---------------|------------|---------|
| John Doe      | HR         | 50000.00|
| Jane Smith    | IT         | 60000.00|
| Mike Johnson  | HR         | 55000.00|
| Emily Davis   | IT         | 65000.00|
| David Brown   | Finance    | 70000.00|
| Temp Employee | HR         | 52000.00|

### One-to-Many Relationship

In a one-to-many relationship, one record in a table can be associated with multiple records in another table. For example, one department can have many employees.
[Index](#index)

#### Create Tables

```sql
-- Create the Departments table
CREATE TABLE Departments (
    DepartmentID INT PRIMARY KEY,
    DepartmentName VARCHAR(100)
);

-- Create the Employees table with a foreign key referencing Departments
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    Name VARCHAR(100),
    Age INT,
    DepartmentID INT,
    Salary DECIMAL(10, 2),
    FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID)
);
```

#### Physical View

**Departments Table**

| DepartmentID | DepartmentName |
|--------------|----------------|
| 1            | HR             |
| 2            | IT             |
| 3            | Finance        |

**Employees Table**

| EmployeeID | Name       | Age | DepartmentID | Salary |
|------------|------------|-----|--------------|--------|
| 1          | John Doe   | 30  | 1            | 50000  |
| 2          | Jane Smith | 25  | 2            | 60000  |
| 3          | Bob Brown  | 28  | 3            | 55000  |

### Many-to-Many Relationship

In a many-to-many relationship, records in one table can be associated with multiple records in another table, and vice versa. For example, employees can be part of multiple projects, and projects can have multiple employees.
[Index](#index)

#### Create Tables

```sql
-- Create the Employees table
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    Name VARCHAR(100),
    Age INT,
    Salary DECIMAL(10, 2)
);

-- Create the Projects table
CREATE TABLE Projects (
    ProjectID INT PRIMARY KEY,
    ProjectName VARCHAR(100)
);

-- Create the EmployeeProjects table to handle the many-to-many relationship
CREATE TABLE EmployeeProjects (
    EmployeeID INT,
    ProjectID INT,
    PRIMARY KEY (EmployeeID, ProjectID),
    FOREIGN KEY (EmployeeID) REFERENCES Employees(EmployeeID),
    FOREIGN KEY (ProjectID) REFERENCES Projects(ProjectID)
);
```

#### Physical View

**Employees Table**

| EmployeeID | Name       | Age | Salary |
|------------|------------|-----|--------|
| 1          | John Doe   | 30  | 50000  |
| 2          | Jane Smith | 25  | 60000  |
| 3          | Bob Brown  | 28  | 55000  |

**Projects Table**

| ProjectID | ProjectName |
|-----------|-------------|
| 1         | Project A   |
| 2         | Project B   |
| 3         | Project C   |

**EmployeeProjects Table**

| EmployeeID | ProjectID |
|------------|-----------|
| 1          | 1         |
| 1          | 2         |
| 2          | 2         |
| 3          | 3         |



```

