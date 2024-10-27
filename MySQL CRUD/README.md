
# CRUD Operations Documentation

## Table of Contents

1. [Create](#create)
2. [Read](#read)
3. [Update](#update)
4. [Delete](#delete)

## Create

### Example

```sql
CREATE DATABASE shirts_db;

USE shirts_db;

CREATE TABLE shirts (
    shirt_id INT AUTO_INCREMENT PRIMARY KEY,
    article VARCHAR(50),
    color VARCHAR(50),
    shirt_size VARCHAR(5),
    last_worn INT
);

DESC shirts;

INSERT INTO shirts (article, color, shirt_size, last_worn)
VALUES
    ('t-shirt', 'white', 'S', 10),
    ('t-shirt', 'green', 'S', 200),
    ('polo shirt', 'black', 'M', 10),
    ('tank top', 'blue', 'S', 50),
    ('t-shirt', 'pink', 'S', 0),
    ('polo shirt', 'red', 'M', 5),
    ('tank top', 'white', 'S', 200),
    ('tank top', 'blue', 'M', 15);

INSERT INTO shirts (article, color, shirt_size, last_worn)
VALUES ('polo shirt', 'purple', 'M', 50);
```

### Questions

1. What does the `DESC shirts;` command do?
2. How many records are inserted into the `shirts` table?
3. What is the primary key of the `shirts` table?

## Read

### Example

```sql
SELECT article, color FROM shirts;

SELECT * FROM shirts WHERE shirt_size='M';

SELECT article, color, shirt_size, last_worn FROM shirts WHERE shirt_size='M';
```

### Questions

1. What columns are selected in the first query?
2. What condition is used in the second query?
3. How many columns are selected in the third query?

## Update

### Example

```sql
UPDATE shirts
SET
    shirt_size = 'L'
WHERE
    article = 'polo shirt';

UPDATE shirts
SET
    last_worn = 0
WHERE
    last_worn = 15;

UPDATE shirts
SET
    color = 'off white',
    shirt_size = 'XS'
WHERE
    color = 'white';
```

### Questions

1. What is the new size for all `polo shirt` articles?
2. What is the new value for `last_worn` where it was previously 15?
3. What changes are made to the records where the color was `white`?

## Delete

### Example

```sql
SELECT * FROM shirts WHERE last_worn=200;

DELETE FROM shirts WHERE last_worn=200;

SELECT * FROM shirts WHERE article='tank top';

DELETE FROM shirts WHERE article='tank top';

SELECT * FROM shirts;

DELETE FROM shirts;

DROP TABLE shirts;

SHOW TABLES;

DESC shirts;
```

### Questions

1. What records are deleted in the first `DELETE` statement?
2. What records are deleted in the second `DELETE` statement?
3. What happens when the `DELETE FROM shirts;` statement is executed?
4. What does the `DROP TABLE shirts;` command do?
5. What does the `SHOW TABLES;` command do?
6. What happens when the `DESC shirts;` command is executed after the table is dropped?

## Summary

- **Create**: Adds new records to the database.
- **Read**: Retrieves records from the database.
- **Update**: Modifies existing records in the database.
- **Delete**: Removes records from the database.

This documentation provides a basic overview and examples of CRUD operations for a `shirts` table in a `shirts_db` database. The questions help in understanding the purpose and outcome of each operation.
