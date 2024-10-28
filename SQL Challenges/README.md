# SQL Challenges Exercises, Practice, Solution


<details>

<summary>
  Problem :  Query to find the maximum salary in each department 
</summary>

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

</details>



<details>

<summary> Problem : Find the information on each salesperson of ABC Company</summary>

### Input Tables

#### Table: salespersons
```sql
CREATE TABLE salespersons (
    salesperson_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50)
);

INSERT INTO salespersons (salesperson_id, first_name, last_name) VALUES
(1, 'Green', 'Wright'),
(2, 'Jones', 'Collins'),
(3, 'Bryant', 'Davis');
```

#### Table: address
```sql
CREATE TABLE address (
    address_id INT PRIMARY KEY,
    salesperson_id INT,
    city VARCHAR(50),
    state VARCHAR(50),
    country VARCHAR(50),
    FOREIGN KEY (salesperson_id) REFERENCES salespersons(salesperson_id)
);

INSERT INTO address (address_id, salesperson_id, city, state, country) VALUES
(1, 2, 'Los Angeles', 'California', 'USA'),
(2, 3, 'Denver', 'Colorado', 'USA'),
(3, 1, 'Atlanta', 'Georgia', 'USA');
```

### SQL Query
```sql
SELECT
    s.first_name,
    s.last_name,
    a.city,
    a.state,
    a.country
FROM
    salespersons s
LEFT JOIN
    address a
ON
    s.salesperson_id = a.salesperson_id;
```

### Output
```markdown
+------------+-----------+-------------+------------+
| first_name | last_name | city        | state      |
+------------+-----------+-------------+------------+
| Green      | Wright    | Atlanta     | Georgia    |
| Jones      | Collins   | Los Angeles | California |
| Bryant     | Davis     | Denver      | Colorado   |
+------------+-----------+-------------+------------+
```

</details>


<details>
<summary>Problem : Find the Second highest sale. Return sale amount.</summary>

### Create table and Insert data
```sql
CREATE TABLE If Not Exists salemast(sale_id int, employee_id int, sale_date date, sale_amt int);
TRUNCATE TABLE salemast;
INSERT INTO salemast (sale_id, employee_id, sale_date, sale_amt) VALUES ('1', '1000', '2012-03-08', 4500);
INSERT INTO salemast (sale_id, employee_id, sale_date, sale_amt) VALUES ('2', '1001', '2012-03-09', 5500);
INSERT INTO salemast (sale_id, employee_id, sale_date, sale_amt) VALUES ('3', '1003', '2012-04-10', 3500); 
INSERT INTO salemast (sale_id, employee_id, sale_date, sale_amt) VALUES ('3', '1003', '2012-04-10', 5500); 

SELECT  * FROM salemast;

```

### view table
```
+---------+-------------+------------+----------+
| sale_id | employee_id | sale_date  | sale_amt |
+---------+-------------+------------+----------+
|       1 |        1000 | 2012-03-08 |     4500 |
|       2 |        1001 | 2012-03-09 |     5500 |
|       3 |        1003 | 2012-04-10 |     3500 |
|       3 |        1003 | 2012-04-10 |     5500 |
+---------+-------------+------------+----------+
```

### SQL Query

```sql 

SELECT 
  sale_amt AS SecondHighestSale
FROM 
  salemast
ORDER BY 
  sale_amt DESC
LIMIT 1 OFFSET 1;


SELECT 
  MAX(sale_amt) AS SecondHighestSale
FROM 
  salemast
WHERE 
  sale_amt < 
  (SELECT 
     MAX(sale_amt) 
   FROM 
     salemast);

SELECT 
  MAX(sale_amt) AS SecondHighestSale
FROM 
  (SELECT DISTINCT sale_amt 
   FROM salemast 
   ORDER BY sale_amt DESC 
   LIMIT 2 OFFSET 1) AS sale;
```
### Output
```
+-------------------+
| SecondHighestSale |
+-------------------+
|              5500 |
+-------------------+
+-------------------+
| SecondHighestSale |
+-------------------+
|              4500 |
+-------------------+
+-------------------+
| SecondHighestSale |
+-------------------+
|              4500 |
+-------------------+
```

</details>

<details>
<summary>  Problem : write a SQL query to find the marks, which appear at least thrice one after another without interruption. Return the number
</summary>

```sql
CREATE TABLE IF NOT EXISTS logs (student_id int, marks int);
TRUNCATE TABLE logs;
INSERT INTO logs (student_id, marks) VALUES ('101', '83');
INSERT INTO logs (student_id, marks) VALUES ('102', '79');
INSERT INTO logs (student_id, marks) VALUES ('103', '83');
INSERT INTO logs (student_id, marks) VALUES ('104', '83');
INSERT INTO logs (student_id, marks) VALUES ('105', '83');
INSERT INTO logs (student_id, marks) VALUES ('106', '79');
INSERT INTO logs (student_id, marks) VALUES ('107', '79');
INSERT INTO logs (student_id, marks) VALUES ('108', '83');
select * from logs;

```
 Show Table
 ```
 +------------+-------+
| student_id | marks |
+------------+-------+
|        101 |    83 |
|        102 |    79 |
|        103 |    83 |
|        104 |    83 |
|        105 |    83 |
|        106 |    79 |
|        107 |    79 |
|        108 |    83 |
+------------+-------+
 ```

### SQL Query

```sql
SELECT DISTINCT l1.marks AS ConsecutiveNums
FROM 
  logs l1
JOIN 
  logs l2 
  ON l1.marks = l2.marks 
  AND l1.student_id = l2.student_id - 1
JOIN 
  logs l3 
  ON l1.marks = l3.marks 
  AND l1.student_id = l3.student_id - 2;
```


### Output
```
+-----------------+
| ConsecutiveNums |
+-----------------+
|              83 |
+-----------------+
```
</details>

<details>
<summary>
Problem : Find all the duplicate emails (no upper case letters) of the employees. Return email id
</summary>

 ### Create Table And Insert Data

```sql
CREATE TABLE IF NOT EXISTS employees(employee_id int, employee_name varchar(255), email_id varchar(255));
TRUNCATE TABLE employees;
INSERT INTO employees (employee_id,employee_name, email_id) VALUES ('101','Liam Alton', 'li.al@abc.com');
INSERT INTO employees (employee_id,employee_name, email_id) VALUES ('102','Josh Day', 'jo.da@abc.com');
INSERT INTO employees (employee_id,employee_name, email_id) VALUES ('103','Sean Mann', 'se.ma@abc.com');	
INSERT INTO employees (employee_id,employee_name, email_id) VALUES ('104','Evan Blake', 'ev.bl@abc.com');
INSERT INTO employees (employee_id,employee_name, email_id) VALUES ('105','Toby Scott', 'jo.da@abc.com');
SELECT * FROM employees;

```

### Show Table

```
+-------------+---------------+---------------+
| employee_id | employee_name | email_id      |
+-------------+---------------+---------------+
|         101 | Liam Alton    | li.al@abc.com |
|         102 | Josh Day      | jo.da@abc.com |
|         103 | Sean Mann     | se.ma@abc.com |
|         104 | Evan Blake    | ev.bl@abc.com |
|         105 | Toby Scott    | jo.da@abc.com |
+-------------+---------------+---------------+
```
### Solution

```sql
-- solve 1
SELECT 
  email_id , 
  count(email_id) as CountEmail 
FROM 
  employees 
group by 
  email_id
having
count(email_id)>1;

-- solve 2
SELECT 
  email_id
FROM 
  (
    SELECT email_id , count(email_id) as CountEmail FROM employees group by email_id
  ) AS subquery
where
  CountEmail>1;

-- solve 3
SELECT DISTINCT p1.email_id
FROM employees p1, employees p2
WHERE p1.email_id = p2.email_id AND p1.employee_id != p2.employee_id;


```

### Output

```
+---------------+
| email_id      |
+---------------+
| jo.da@abc.com |
+---------------+
```

</details>

 <!-- problem : Find those customers who never ordered anything -->
<details>
<summary>
Problem : Find those customers who never ordered anything
</summary>

 ### Create Table And Insert Data

```sql
CREATE TABLE IF NOT EXISTS customers (customer_id int, customer_name varchar(255));
TRUNCATE TABLE customers;
INSERT INTO customers (customer_id, customer_name) VALUES ('101', 'Liam');
INSERT INTO customers (customer_id, customer_name) VALUES ('102', 'Josh');
INSERT INTO customers (customer_id, customer_name) VALUES ('103', 'Sean');
INSERT INTO customers (customer_id, customer_name) VALUES ('104', 'Evan');
INSERT INTO customers (customer_id, customer_name) VALUES ('105', 'Toby');	
CREATE TABLE IF NOT EXISTS orders (order_id int, customer_id int, order_date Date, order_amount int);
TRUNCATE TABLE orders;
INSERT INTO orders (order_id, customer_id,order_date,order_amount) VALUES ('401', '103','2012-03-08','4500');
INSERT INTO orders (order_id, customer_id,order_date,order_amount) VALUES ('402', '101','2012-09-15','3650');
INSERT INTO orders (order_id, customer_id,order_date,order_amount) VALUES ('403', '102','2012-06-27','4800');
INSERT INTO orders (order_id, customer_id,order_date,order_amount) VALUES ('404', '102','2012-06-27','4800');
SELECT * FROM customers;
SELECT * FROM orders;


```

### Show Table

```
+-------------+---------------+
| customer_id | customer_name |
+-------------+---------------+
|         101 | Liam          |  
|         102 | Josh          |
|         103 | Sean          |
|         104 | Evan          |
|         105 | Toby          |
+-------------+---------------+

+----------+-------------+------------+--------------+
| order_id | customer_id | order_date | order_amount |
+----------+-------------+------------+--------------+
|      401 |         103 | 2012-03-08 |         4500 |
|      402 |         101 | 2012-09-15 |         3650 |
|      403 |         102 | 2012-06-27 |         4800 |
|      404 |         102 | 2012-06-27 |         4800 |
+----------+-------------+------------+--------------+
```
### Solution

```sql
-- solve 1
SELECT
  c.customer_id,
  c.customer_name
FROM 
  customers c 
LEFT JOIN 
  orders o ON c.customer_id = o.customer_id 
WHERE 
  o.customer_id IS NULL;

-- solve 2

SELECT 
  customer_id,
  customer_name
FROM 
  customers
WHERE (customer_id) not IN 
  (
    SELECT customer_id from orders
  );
  
  --solve 3

SELECT
  customer_id,
  customer_name 
FROM customers WHERE 0 = 
(
SELECT COUNT(*) FROM orders 
WHERE customers.customer_id = orders.customer_id
);


```

### Output

```
+-------------+---------------+
| customer_id | customer_name |
+-------------+---------------+
|         104 | Evan          |
|         105 | Toby          |
+-------------+---------------+
```

</details>

<!-- Problem: SQL query to remove all the duplicate emails of employees keeping the unique email with the lowest employee id. Return employee id and unique emails -->

<details>
<summary>
Problem : Remove all the duplicate emails of employees keeping the unique email with the lowest employee id. Return employee id and unique emails
</summary>

 ### Create Table And Insert Data

```sql
CREATE TABLE IF NOT EXISTS employees(employee_id int, employee_name varchar(255), email_id varchar(255));
TRUNCATE TABLE employees;
INSERT INTO employees (employee_id,employee_name, email_id) VALUES ('101','Liam Alton', 'li.al@abc.com');
INSERT INTO employees (employee_id,employee_name, email_id) VALUES ('102','Josh Day', 'jo.da@abc.com');
INSERT INTO employees (employee_id,employee_name, email_id) VALUES ('103','Sean Mann', 'se.ma@abc.com');	
INSERT INTO employees (employee_id,employee_name, email_id) VALUES ('104','Evan Blake', 'ev.bl@abc.com');
INSERT INTO employees (employee_id,employee_name, email_id) VALUES ('105','Toby Scott', 'jo.da@abc.com');
SELECT * FROM employees;


```

### Show Table

```
+-------------+---------------+---------------+
| employee_id | employee_name | email_id      |
+-------------+---------------+---------------+
|         101 | Liam Alton    | li.al@abc.com |
|         102 | Josh Day      | jo.da@abc.com |
|         103 | Sean Mann     | se.ma@abc.com |
|         104 | Evan Blake    | ev.bl@abc.com |
|         105 | Toby Scott    | jo.da@abc.com |
+-------------+---------------+---------------+
```
### Solution

```sql

DELETE e1 FROM employees e1,  employees e2
WHERE
    e1.email_id = e2.email_id AND e1.employee_id > e2.employee_id;
    
    
SELECT * FROM employees;

```

### Output

```
+-------------+---------------+---------------+
| employee_id | employee_name | email_id      |
+-------------+---------------+---------------+
|         101 | Liam Alton    | li.al@abc.com |
|         102 | Josh Day      | jo.da@abc.com |
|         103 | Sean Mann     | se.ma@abc.com |
|         104 | Evan Blake    | ev.bl@abc.com |
+-------------+---------------+---------------+
```

</details>

 <!-- Find all dates' city ID with higher pollution compared to its previous dates (yesterday). Return city ID, date and pollution.  -->

<details>
<summary>
Problem :  Find all dates' city ID with higher pollution compared to its previous dates (yesterday). Return city ID, date and pollution.
</summary>

 ### Create Table And Insert Data

```sql
CREATE TABLE IF NOT EXISTS so2_pollution (city_id int, date date, so2_amt int);
TRUNCATE TABLE so2_pollution;
INSERT INTO so2_pollution (city_id, date, so2_amt) VALUES ('701', '2015-10-15', '5');
INSERT INTO so2_pollution (city_id, date, so2_amt) VALUES ('702', '2015-10-16', '7');
INSERT INTO so2_pollution (city_id, date, so2_amt) VALUES ('703', '2015-10-17', '9');
INSERT INTO so2_pollution (city_id, date, so2_amt) VALUES ('704', '2018-10-18', '15');
INSERT INTO so2_pollution (city_id, date, so2_amt) VALUES ('705', '2015-10-19', '14');
SELECT * FROM so2_pollution;

```

### Show Table

```
+---------+------------+---------+
| city_id | date       | so2_amt |
+---------+------------+---------+
|     701 | 2015-10-15 |       5 |
|     702 | 2015-10-16 |       7 |
|     703 | 2015-10-17 |       9 |
|     704 | 2018-10-18 |      15 |
|     705 | 2015-10-19 |      14 |
+---------+------------+---------+
```
### Solution

```sql
-- solve 1
SELECT 
  s1.city_id 
from
  so2_pollution s1 
join  
  so2_pollution s2
on s1.date=ADDDATE(s2.date,1) and s1.so2_amt>s2.so2_amt;

-- solve 2 
SELECT 
  s1.city_id 
from
  so2_pollution s1 
join  
  so2_pollution s2
on DATEDIFF(s1.date,s2.date)=1 and s1.so2_amt>s2.so2_amt;

```

### Output

```
+---------+
| city_id |
+---------+
|     702 |
|     703 |
+---------+
```

</details>
