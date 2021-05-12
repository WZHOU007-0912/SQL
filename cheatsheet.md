# PostgreSQL

## Design Database

Database builders prefer to organize data using separate tables for each main entity.

1. Identify all the data elements that need to be stored in the databse

2. Break complex elements down into smaller components whenever that maskes sense (divide data elements as much as possible)
3. Identify the tables that will make up the system and to determine which data elements are assigned as columns in each table
4. Define the relationships between the tables by identifying the primary and foreign keys:
  
   - each table should have a primary key that uniquely identifies each row

   - if a suitable column doesn't exist for a primary key, can create an ID column that is incremented by one for each row as the primary key
  
   - if two tables have a one-to-mamy relationship, may need to add a foreign key column to that table on the 'many' side and the foreign key must have the same data type as the primary key column it's related to
  
   - if it is many-to-many relationship, will need to define a linking table to relate them (the linking table doesn't usually have a primary key)
  
   - if two tables have a one-to-one relationship, they should be related by their primary keys

5. Normalize the database to reduce data redundancy
6. Identify the indexs that are needed for each table

## Create Database,Tables, and Indexs

### Create database

#### only if the databse name dosen't exist

```sql
CREATE DATABASE IF NOT EXISTS db_name

```

### Drop the database if exist

```sql
DROP DATABASE IF EXIST db_name
```

### select the specified database make it as current one

```sql
USE db_name
```

### Create table

```sql
CREATE TABLE table_name
(
column_name data_type [column_attribute] [column_level_constraint]
)
```

### Add a new column

```sql
ALTER TABLE [db_name.]table_name
{
ADD column_name data_type [cloumn_attribute]
}
```

### Drop a column

```sql
ALTER TABLE [db_name.]table_name
{
DROP column_name
}
```

### Change the length / type / default value of a column

if you don't include an existing attribute for a column, that attribute would be dropped from the column definition.
The exception are attributes that define indexes, including the `PRIMARY KEY` and `UNIQUE` attributes.

```sql
ALTER TABLE [db_name.]table_name
{
MODIFY column_name data_type [column_attribute]
}
```

### Change the name of a column

```sql
ALTER TABLE [db_name.]table_name
{
RENAME column_name TO new_column_name
}
```

### Add a primary key constraint

```sql
ALTER TABLE [db_name.]table_name
ADD PRIMARY KEY (column_name)
```

### Add a foreign key constraint

```sql
ALTER TABLE [db_name.]table_name
ADD CONSTRAINT constraint_name 
FOREIGN KEY (column_name) REFERENCES table_name (column_name)
```

### Drop a primary key constraint

```sql
ALTER TABLE [db_name.]table_name
DROP PRIMARY KEY
```

### Drop a foreign key constraint

```sql
ALTER TABLE [db_name.]table_name
DROP FOREIGN KEY column_name
```

### Rename a table

```sql
RENAME TABLE table_name TO new_table_name
```

### Delete a row from a table

```sql
DELETE FROM table_name
WHERE expression
```

### Delete all rows/data from a table

```sql
DELETE FROM table_name
```

### Delete a table from the database

```sql
DROP TABLE [db_name.]table_name
```

### Create an (unique) index based on a single column

```sql
CREATE UNIQUE INDEX index_name
       ON table_name (column_name)
```

### Create an index based on two columns

```sql
CREATE INDEX index_name
       ON table_name (column_name1, column_name2)
```

### Create an index that’s sorted in descending order

```sql
CREATE UNIQUE INDEX index_name
       ON table_name (column_name DESC)
```

### Drop an index

```sql
DROP INDEX index_name ON table_name
```

### Insert rows into a Table

```sql
INSERT INTO table_name (first_column, second_column, third_column)
VALUES(value1_1, value1_2, value1_3),
      (value2_1, value2_2, value2_3);
```

do not forget the semicolon at the end of each statement

## Update table values

```sql
UPDATE table_name
SET column = value
```

## Script Example

```sql
-- create the database

DROP DATABASE IF EXISTS ap;
CREATE DATABASE ap;

-- select the databse

USE ap;

-- create the tables

CREATE TABLE general_accounts
(
account_number           INT          PRIMARY KRY,
account_description      VARCHAR(50)  UNIQUE
)

-- create the tables, allow to store an empty string for vendor_address,vendor_city

CREATE TABLE vendors
(
vendor_id                INT          PRIMARY KEY          AUTO_INCREMENT,
vendor_name              VARCHAR(50)  NOT NULL             UNIQUE,
vendor_address           VARCHAR(50),
vendor_city              VARCHAR(50),
default_account_number   INT          NOT NULL,
CONSTRAINT vendor_accounts
FOREIGN KEY (default_account_number)
REFERENCE general_accounts (account_number)
);
```

## Retrive Data

```sql
SELECT select_list
FROM table_source
WHERE search_condition
ORDER BY order_by_list
LIMIT row_limit
```

The `LIMIT` clause retrieves data that is limited to a specified number of rows.  

### CONCAT function to join strings

```sql
SELECT CONCAT(column_name1,',',column_name2) AS new_name
```

### ALL & DISCTINCT keywords

The `ALL` and `DISCTINCT` keywords specify whether or not duplicate rows are return.  
The `DISCTINCT` keyword is used to find the unique values. This technique gives us the ability to ask, 'For each x in the table, what are all the y values?' - 'For each factory, what are all the chemicals it produces?'
To use the `DISTINCT` or `ALL` keyword, code it immediately after the SELECT keyword.

### ORDER BY

`ASC`  
`DESC`  

### Filtering Rows with WHERE

The `WHERE` clause consits of one or more *Boolean expressions* that result in a true, false or null value.  
It allows you to find rows that match a specific value, a range of values, or multiple values based on criteria supplied via an operator.

| Operator  | Function                          |
|-----------|:---------------------------------:|
| =         |Equal to                           |
| <> or !=  | Not equal to                      |
| >         | Greater than                      |
| <         | Less than                         |
| IN        | Match one of a set of values      |
| LIKE      | Match a pattern (case sensitive)  |
| ILIKE     | Match a pattern (case insensitive)|

### Using LIKE and ILIKE with WHERE

- Percent sign (`%`) A wildcard matching one or more characters
- Underscore (`_`) A wildcard matching just one character  
For example, if trying to find the word *baker*, the following `LIKE` partterns will match it:

```sql
LIKE 'b%'
LIKE '%ak%'
LIKE '_aker'
LIKE 'ba_er'
```

Don’t assume that whoever typed the names of people, places, products, or other proper nouns always remembered to capitalize them.
Using a case-insensitive search `ILIKE` would help you find variations.  

## Data Type

### Characters

- `CHAR(n)`
  A fixed-length column where the character length is specified by *n*. (Nowadays, `CHAR(n)` is used infrequently.) if you insert fewer than *n* characters in any row, SQL (PostgreSQL) pads the rest of the column with spaces.
- `VARCHAR(n)`
  A variable-length column where the *maximum* length is specified by *n*. In large databases, this practice saves considerable space.  
- `TEXT`
  A variable-length column of unlimited length.
**Typically, using `VARCHAR` with an *n* value sufficient to handle outliers is a solid strategy.**

### Numbers

- Integers  
  Whole numbers, both positive and negative, including zero.  
  Three integer types:
  1. `SMALLINT`: (-32768 ,+ 32767)
  2. `INTEGER`: (−2147483648 , +2147483647)
  3. `BIGINT`: (−9223372036854775808 , +9223372036854775807)
- Auto-Incrementing Integers  
  When you add a column with a `SERIAL` type, PostgreSQL will auto-increment the value in the column each time you insert a row, **starting with 1**, up to the maximum of each integer type.  
Because the column is auto-incrementing, you don'y need to insert a number into that column when adding data.
- Decimal Numbers
  1. numeric, decimal (fixed-point): `NUMERIC(precision, scale)`
  2. `REAL` (floating): floating-6 decimal digits precision point
  3. `DOUBLE PRECISION` (floating): floating-15 decimal digits precision point

### Dates and Times

- **timestampe with time zone**: records date and time which are useful for a range of situations you might track
- **date**: records only the date
- **time**: records only the time
- **interval**: holds the value representing a unit of time expressed in the format *quantity unit*, e.g.: *12 days*, *8 hours*.

#### Extracting the components of a timestamp value

```sql
date_part(text, value)
```

The function takes two inputs. The first is a string in text format that represents the part of the date or time **to extract**. The second is the *date*, *time*, or *timestamp* value.

```sql
SELECT
    date_part('year', '2019-12-01 18:37:12 EST'::timestamptz) AS "year"
```

### Transforming Values from One Type to Another with CAST

The CAST() function only succeeds when the target data type can accommodate the original value. Casting an integer as text is possible, because the character types can include numbers. Casting text with letters of the alphabet as a number is not.

```sql
SELECT CAST(timestamp_column AS VARCHAR(10))
```

## Importing and Exporting Data

### Working with Delimited Text Files

A delimited text file contains rows of data, and each row represents one row in a table. In each row, a character separates, or delimits, each data column.  
The character that most commonly used in separating each data colimn is the comma; hence we have *comma-separated values* i.e. *CSV* file.
Notice that a comma separates each piece of data — first name, last name, street, town, state, and phone — without any spaces. The commas tell the software to treat each item as a separate column, either upon import or export.

### Using COPY to Import Data

```sql
COPY table_name
FROM file_path
WITH(FORMAT CSV, HEADER)
```

### Using COPY to Export Data

```sql
COPY table_name
TO file_path
WITH (FORMAT CSV, HEADER)
```

1. Input and output file format:  
   Use the `FORMAT` *format_name* option to specify the type of reading/writing file, such as *CSV* and *TEXT*.
2. Presence of a header row:  
   On import, use `HEADER` to specify that source file has a header row and tell the database to **skip** the header, as you don't want the column names in the header to become part of the data in the table.  
   On export, use `HEADER` tells the database to **include** the column names as a header row in the output file.

## Joining types

### INNER JOIN

Returns rows from both tables where matching values are found in the joined columns of both tables.

### LEFT JOIN

Returns every row from the left table plus rows rows that match values in the joined column from the right table. When a left table row dosen't have a match in the right table, the result shows no values from the right table.

### RIGHT JOIN

Returns every row from the right table plus rows that match joined column from the left table.

### FULL OUTER JOIN

Returns every row from both tables and matches rows; then join the rows where values in the joined column.

![full join](https://github.com/WZHOU007-0912/images/blob/master/full_join.png)

### Using NULL to Find Rows with Missing Values

```sql
SELECT column_name
FROM table1_name LEFT JOIN table2_name
ON column1 = column2
WHERE coumn1 IS NULL
```

## Grouping and Summarizing

### HAVING vs. WHERE

For aggregate functins, such as `sum()`, can't be used within a `WHERE` clasue because they operate at a row level, and aggregate functions work across rows.  
The `HAVING` clause places conditions on **groups** created by aggregating. When use `HAVING` clause, insert it **after** `GROUP BY`.

```sql
SELECT table_name
FROM table1_name JOIN table2_name
ON column1 = coumn2
WHERE column_condition
GROUP BY column_name
HAVING [aggregate_function(column_name) condition]
ORDER BY column_name
```

## Ranking: rank() and dense_rank()

The difference between `rank()` and `dense_rank()` is the way they handle the **next rank value** after a **tie**: `rank()` includes a gap in the rank order, but `dense_rank()` does not.

### Ranking within subgroups with PARTITION BY

```sql
SELECT column_name
rank() OVER (PARTITION BY column1_name ORDER BY column2_name)
FROM table_name
```

## Reclassifying value with CASE

```sql
CASE WHEN condition1 THEN result1
     WHEN condition2 THEN result2
     ELSE result3
END
```

