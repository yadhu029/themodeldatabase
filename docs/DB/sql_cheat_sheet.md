# SQL Cheat Sheet (MySQL Focused)

## What is SQL?
SQL stands for **Structured Query Language**, used for accessing and manipulating relational databases.

---

## What Can SQL Do?

- Execute queries against a database
- Retrieve data from a database
- Insert records into a database
- Update existing records
- Delete records from a database
- Create new databases and tables
- Create stored procedures and views
- Set user permissions on database objects

---

## What is RDBMS?

**RDBMS** stands for **Relational Database Management System**.  
Examples: MySQL, MS SQL Server, Oracle, IBM DB2, PostgreSQL, SQLite

- Stores data in **tables** (collections of rows and columns)
- Tables relate to each other using **foreign keys**
- Ensures data integrity via **constraints**

---

## SQL Statements

SQL uses simple English-like syntax:
- Statements end with `;` (especially required in multi-statement environments)
- Case-insensitive keywords (but best practice: uppercase)

---

## Creating Tables

```sql
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    ...
);
```

### Example:

```sql
CREATE TABLE Customers (
    CustomerID INT,
    CustomerName VARCHAR(100),
    ContactName VARCHAR(100),
    Country VARCHAR(50)
);
```

---

## Copying from an Existing Table

```sql
CREATE TABLE new_table AS
SELECT column1, column2
FROM existing_table
WHERE condition;
```

---

## SQL Data Types (MySQL)

### String Data Types

| Data Type       | Description                                                  |
|----------------|--------------------------------------------------------------|
| `CHAR(n)`       | Fixed-length string (0–255 chars)                            |
| `VARCHAR(n)`    | Variable-length string (up to 65535 chars)                   |
| `BINARY(n)`     | Fixed-length binary byte string                              |
| `VARBINARY(n)`  | Variable-length binary byte string                           |
| `TEXT`          | Large text fields (TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT)     |
| `ENUM`          | A string object with a predefined set of values              |
| `SET`           | A string object with zero or more chosen values from a list  |

### Numeric Data Types

| Data Type     | Description                                                   |
|---------------|---------------------------------------------------------------|
| `BIT(n)`       | Bit field (1–64 bits)                                         |
| `TINYINT`      | 1 byte (-128 to 127 / 0 to 255 unsigned)                      |
| `SMALLINT`     | 2 bytes (-32K to 32K / 0 to 65K unsigned)                     |
| `MEDIUMINT`    | 3 bytes                                                       |
| `INT`/`INTEGER`| 4 bytes (-2B to 2B)                                           |
| `BIGINT`       | 8 bytes                                                       |
| `DECIMAL(p,s)` | Exact numeric value (e.g., money)                             |
| `FLOAT`        | Approximate numeric (floating point)                          |
| `DOUBLE`       | Approximate numeric (more precision than FLOAT)               |

### Date and Time Types

| Data Type     | Description                                       |
|---------------|---------------------------------------------------|
| `DATE`         | Format: `YYYY-MM-DD`                              |
| `DATETIME`     | Format: `YYYY-MM-DD HH:MM:SS`                     |
| `TIMESTAMP`    | UNIX timestamp (auto updated by default)          |
| `TIME`         | Format: `HH:MM:SS`                                |
| `YEAR`         | Format: `YYYY` (1901–2155, 0000)                  |

---

## SELECT Queries

### Basic Syntax

```sql
SELECT column1, column2
FROM table_name;
```

### Select All

```sql
SELECT * FROM table_name;
```

### DISTINCT

```sql
SELECT DISTINCT column FROM table_name;
```

### COUNT(DISTINCT ...)

```sql
SELECT COUNT(DISTINCT column_name) FROM table_name;
```

---

## WHERE Clause

Used to filter records.

```sql
SELECT column1 FROM table
WHERE condition;
```

### Operators

| Operator | Meaning                    |
|----------|----------------------------|
| =        | Equal                      |
| <> or != | Not equal                  |
| >        | Greater than               |
| <        | Less than                  |
| >=       | Greater than or equal      |
| <=       | Less than or equal         |
| BETWEEN  | Within a range             |
| LIKE     | Pattern match (`%`, `_`)   |
| IN       | Match any in a set         |
| IS NULL  | Value is null              |
| IS NOT NULL | Value is not null       |

---

## ORDER BY

Used to sort result sets.

```sql
SELECT * FROM table
ORDER BY column1 [ASC|DESC], column2 [ASC|DESC];
```

- Default is ascending (ASC)
- Can sort by multiple columns

---

## Logical Operators

### AND

```sql
SELECT * FROM Customers
WHERE Country = 'Spain' AND CustomerName LIKE 'G%';
```

### OR

```sql
SELECT * FROM Customers
WHERE Country = 'Spain' OR Country = 'Germany';
```

### Combined (AND + OR)

```sql
SELECT * FROM Customers
WHERE Country = 'Spain' AND (CustomerName LIKE 'G%' OR CustomerName LIKE 'R%');
```

---

## Importing Data into SQLite

Before importing, create the table first.

```sql
.mode csv
.separator ","
.import /path/to/file.txt table_name
```

---

## Bonus: Other Useful SQL Features

### Aliases

```sql
SELECT column_name AS alias_name FROM table;
```

### LIMIT

Restricts number of records returned:

```sql
SELECT * FROM Customers
LIMIT 10;
```

### IS NULL / IS NOT NULL

```sql
SELECT * FROM Orders
WHERE ShippedDate IS NULL;
```

### Aggregate Functions

| Function | Purpose                     |
|----------|-----------------------------|
| `COUNT()` | Number of rows             |
| `SUM()`   | Total of numeric column    |
| `AVG()`   | Average value              |
| `MIN()`   | Minimum value              |
| `MAX()`   | Maximum value              |

---

## Table Relationships and Constraints

| Constraint     | Description                                |
|----------------|--------------------------------------------|
| `PRIMARY KEY`  | Uniquely identifies each record             |
| `FOREIGN KEY`  | Links to a primary key in another table     |
| `UNIQUE`       | Ensures all values in a column are unique   |
| `NOT NULL`     | Ensures a column cannot have NULL values    |
| `DEFAULT`      | Sets a default value for a column           |
| `CHECK`        | Ensures values satisfy a condition          |
