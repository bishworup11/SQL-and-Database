# MySQL Aggregation Functions and Window Functions Documentation

## Example Table

Let's consider the following example table named `sales`:

```sql
CREATE TABLE sales (
    id INT AUTO_INCREMENT PRIMARY KEY,
    product_name VARCHAR(50),
    category VARCHAR(50),
    price DECIMAL(10, 2),
    sale_date DATE,
    quantity INT
);
```

Here's a physical view of the `sales` table with some sample data:

| id | product_name | category | price | sale_date | quantity |
|---:|:-------------|:---------|------:|:----------|---------:|
|  1 | Product A    | Category1|  10.00| 2022-01-01|         2 |
|  2 | Product B    | Category2|  15.00| 2022-01-01|         1 |
|  3 | Product C    | Category1|  20.00| 2022-01-02|         3 |
|  4 | Product A    | Category1|  10.00| 2022-01-02|         1 |
|  5 | Product D    | Category3|  30.00| 2022-01-03|         2 |



## Aggregation Functions

Aggregation functions perform calculations on a set of rows and return a single value. Here are some commonly used aggregation functions in MySQL:

### 1. `COUNT()`

Syntax:
```sql
COUNT(column_name)
```

Example: Count the total number of sales records.
```sql
SELECT COUNT(*) AS total_sales FROM sales;
```

Physical view of the result:

| total_sales |
|------------:|
|           5 |

### 2. `SUM()`

Syntax:
```sql
SUM(column_name)
```

Example: Calculate the total sales amount.
```sql
SELECT SUM(price * quantity) AS total_sales_amount FROM sales;
```

Physical view of the result:

| total_sales_amount |
|-------------------:|
|             130.00 |

### 3. `AVG()`

Syntax:
```sql
AVG(column_name)
```

Example: Calculate the average price of products.
```sql
SELECT AVG(price) AS average_price FROM sales;
```

Physical view of the result:

| average_price |
|--------------:|
|          19.00 |

### 4. `MIN()`

Syntax:
```sql
MIN(column_name)
```

Example: Find the minimum price of products.
```sql
SELECT MIN(price) AS minimum_price FROM sales;
```

Physical view of the result:

| minimum_price |
|--------------:|
|          10.00 |

### 5. `MAX()`

Syntax:
```sql
MAX(column_name)
```

Example: Find the maximum price of products.
```sql
SELECT MAX(price) AS maximum_price FROM sales;
```

Physical view of the result:

| maximum_price |
|--------------:|
|          30.00 |

## Window Functions

Window functions perform calculations across a set of table rows that are somehow related to the current row. Here are some commonly used window functions in MySQL:


#### Sample Data

Let's assume we have the following `sales` table:

| id | product_name | category | price | sale_date | quantity |
|---:|:-------------|:---------|------:|:----------|---------:|
|  1 | Product A    | Category1|  10.00| 2022-01-01|         2 |
|  2 | Product B    | Category1|  15.00| 2022-01-01|         2 |
|  3 | Product C    | Category1|  20.00| 2022-01-02|         3 |
|  4 | Product A    | Category1|  10.00| 2022-01-02|         1 |
|  5 | Product D    | Category2|  30.00| 2022-01-03|         2 |
|  6 | Product E    | Category2|  25.00| 2022-01-03|         2 |
|  7 | Product F    | Category2|  25.00| 2022-01-03|         2 |

### 1. `ROW_NUMBER()`

Syntax:
```sql
ROW_NUMBER() OVER ([PARTITION BY clause] [ORDER BY clause])
```

Example: Assign a unique row number to each row within each category, ordered by sale_date.
```sql
SELECT
    *,
    ROW_NUMBER() OVER (PARTITION BY category ORDER BY sale_date) AS row_num
FROM
    sales;
```

Physical view of the result:

| id | product_name | category | price | sale_date | quantity | row_num |
|---:|:-------------|:---------|------:|:----------|---------:|--------:|
|  1 | Product A    | Category1|  10.00| 2022-01-01|         2 |       1 |
|  2 | Product B    | Category1|  15.00| 2022-01-01|         2 |       2 |
|  3 | Product C    | Category1|  20.00| 2022-01-02|         3 |       3 |
|  4 | Product A    | Category1|  10.00| 2022-01-02|         1 |       4 |
|  5 | Product D    | Category2|  30.00| 2022-01-03|         2 |       1 |
|  6 | Product E    | Category2|  25.00| 2022-01-03|         2 |       2 |
|  7 | Product F    | Category2|  25.00| 2022-01-03|         2 |       3 |

### 2. `RANK()`

Syntax:
```sql
RANK() OVER ([PARTITION BY clause] [ORDER BY clause])
```

Example: Rank products based on their total sales quantity within each category.
```sql
SELECT
    product_name,
    category,
    SUM(quantity) AS total_quantity,
    RANK() OVER (PARTITION BY category ORDER BY SUM(quantity) DESC) AS rank
FROM
    sales
GROUP BY
    product_name,
    category;
```

Physical view of the result:

| product_name | category | total_quantity | rank |
|:-------------|:---------|---------------:|-----:|
| Product C    | Category1|               3 |    1 |
| Product A    | Category1|               3 |    1 |
| Product B    | Category1|               2 |    3 |
| Product D    | Category2|               2 |    1 |
| Product E    | Category2|               2 |    1 |
| Product F    | Category2|               2 |    1 |

### 3. `DENSE_RANK()`

Syntax:
```sql
DENSE_RANK() OVER ([PARTITION BY clause] [ORDER BY clause])
```

Example: Rank products based on their total sales quantity within each category, but without gaps in ranking.
```sql
SELECT
    product_name,
    category,
    SUM(quantity) AS total_quantity,
    DENSE_RANK() OVER (PARTITION BY category ORDER BY SUM(quantity) DESC) AS dense_rank
FROM
    sales
GROUP BY
    product_name,
    category;
```

Physical view of the result:

| product_name | category | total_quantity | dense_rank |
|:-------------|:---------|---------------:|-----------:|
| Product C    | Category1|               3 |          1 |
| Product A    | Category1|               3 |          1 |
| Product B    | Category1|               2 |          2 |
| Product D    | Category2|               2 |          1 |
| Product E    | Category2|               2 |          1 |
| Product F    | Category2|               2 |          1 |

### Summary of Differences

- **`ROW_NUMBER()`**: Assigns a unique sequential integer to rows within a partition of a result set. Each row gets a unique number.
- **`RANK()`**: Assigns a rank to rows within a partition of a result set, with possible gaps in ranking. Rows with the same value get the same rank, and the next rank skips the number of tied rows.
- **`DENSE_RANK()`**: Assigns a rank to rows within a partition of a result set, without gaps in ranking. Rows with the same value get the same rank, and the next rank is incremented by one.

These examples should help illustrate the differences between `ROW_NUMBER()`, `RANK()`, and `DENSE_RANK()` in a clear and concise manner.


### 3. `SUM()` as a window function

Syntax:
```sql
SUM(column_name) OVER ([PARTITION BY clause] [ORDER BY clause] [frame_clause])
```

Example: Calculate the running total of sales quantity within each category, ordered by sale_date.
```sql
SELECT
    *,
    SUM(quantity) OVER (PARTITION BY category ORDER BY sale_date) AS running_total
FROM
    sales;
```

Physical view of the result:

| id | product_name | category | price | sale_date | quantity | running_total |
|---:|:-------------|:---------|------:|:----------|---------:|--------------:|
|  1 | Product A    | Category1|  10.00| 2022-01-01|         2 |             2 |
|  3 | Product C    | Category1|  20.00| 2022-01-02|         3 |             5 |
|  4 | Product A    | Category1|  10.00| 2022-01-02|         1 |             6 |
|  2 | Product B    | Category2|  15.00| 2022-01-01|         1 |             1 |
|  5 | Product D    | Category3|  30.00| 2022-01-03|         2 |             2 |

## Additional Problems and Solutions

### Problem 1: Find the top 3 products by total sales amount.

Solution using aggregation and window functions:
```sql
SELECT
    product_name,
    SUM(price * quantity) AS total_sales_amount,
    RANK() OVER (ORDER BY SUM(price * quantity) DESC) AS rank
FROM
    sales
GROUP BY
    product_name
HAVING
    rank <= 3;
```

Physical view of the result:

| product_name | total_sales_amount | rank |
|:-------------|-------------------:|-----:|
| Product D    |              60.00 |    1 |
| Product C    |              60.00 |    1 |
| Product A    |              40.00 |    3 |

### Problem 2: Calculate the 7-day moving average of daily sales amount.

Solution using window functions with a frame clause:
```sql
WITH daily_sales AS (
  SELECT
    sale_date,
    SUM(price * quantity) AS daily_sales_amount
  FROM
    sales
  GROUP BY
    sale_date
)
SELECT
  sale_date,
  daily_sales_amount,
  AVG(daily_sales_amount) OVER (ORDER BY sale_date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS moving_avg
FROM
  daily_sales;
```

Physical view of the result:

| sale_date  | daily_sales_amount | moving_avg |
|:-----------|-------------------:|-----------:|
| 2022-01-01 |              40.00 |      40.00 |
| 2022-01-02 |              50.00 |      45.00 |
| 2022-01-03 |              60.00 |      50.00 |

### Problem 3: Find the percentage of sales quantity by category.

Solution using aggregation and window functions:
```sql
SELECT
    category,
    SUM(quantity) AS total_quantity,
    SUM(quantity) * 100.0 / SUM(SUM(quantity)) OVER () AS percentage
FROM
    sales
GROUP BY
    category;
```

Physical view of the result:

| category | total_quantity | percentage |
|:---------|---------------:|----------:|
| Category1|               6 |      60.00 |
| Category2|               1 |      10.00 |
| Category3|               2 |      20.00 |

Window functions provide powerful tools for analyzing data across related rows, while aggregation functions help summarize data based on specific criteria. Choosing the right function depends on the specific problem you're trying to solve. By combining these functions, you can gain valuable insights from your data.

### Problem 4: Query to find the maximum salary in each department

Create table

```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    dept VARCHAR(100),
    salary DECIMAL(10, 2)
);

-- Insert sample data
INSERT INTO employees (id, name, dept, salary) VALUES
(1, 'Alice', 'HR', 5000),
(2, 'Bob', 'HR', 6000),
(3, 'Charlie', 'IT', 7000),
(4, 'David', 'IT', 7500),
(5, 'Eva', 'Finance', 8000),
(6, 'Frank', 'Finance', 7500);

```
Solution 1:Using Aggregation Function

```sql
SELECT name, dept, salary
FROM Employees
WHERE (dept, salary) IN
    (
        SELECT dept, MAX(salary) AS salary
        FROM Employees
        GROUP BY dept
    );

```

Solution 2:Using Aggregation Function Wiht Join

```sql
SELECT e.name, e.dept, e.salary
FROM employees e
JOIN (
    SELECT dept, MAX(salary) AS max_salary
    FROM employees
    GROUP BY dept
) d ON e.dept = d.dept AND e.salary = d.max_salary;

```

Solution 3: Using Window Function

```sql
WITH table1 AS (
    SELECT 
        id,
        name,
        dept,
        salary,
        MAX(salary) OVER (PARTITION BY dept ORDER BY salary DESC) AS MaxSalary,
        RANK() OVER (PARTITION BY dept ORDER BY salary DESC) AS rankNumber,
        ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salary DESC) AS rowNumber,
        DENSE_RANK() OVER (PARTITION BY dept ORDER BY salary DESC) AS denseRank
    FROM employees
)
SELECT id, name, dept, salary 
FROM table1 
WHERE rowNumber = 1;

```

## Execution Ranking

Before diving into the functions, it's important to understand the execution ranking of SQL queries. The order of execution is as follows:

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


```sql
WITH filtered_data AS (
    SELECT
        column1,
        column2
    FROM
        table1
    JOIN
        table2 ON table1.id = table2.id
    WHERE
        column1 > 10
),
grouped_data AS (
    SELECT
        column2,
        SUM(column1) AS sum_column1
    FROM
        filtered_data
    GROUP BY
        column2
    HAVING
        COUNT(column2) > 5
),
windowed_data AS (
    SELECT
        column1,
        column2,
        ROW_NUMBER() OVER (PARTITION BY column2 ORDER BY column1) AS row_num
    FROM
        filtered_data
)
SELECT DISTINCT
    column1,
    column2,
    row_num
FROM
    windowed_data
ORDER BY
    column1
LIMIT 10
OFFSET 5;
```

Here's a breakdown of the query:

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

This query uses Common Table Expressions (CTEs) to break down the steps into more manageable parts, ensuring that each step is logically separated and correctly applied.
Window functions are executed after the `HAVING` clause and before the `SELECT` clause.