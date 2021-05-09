# Design Database
## Design data structure
Database builders prefer to organize data using separate tables for each main entity the database manages in order to reduce redundant data.  
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

# Create Database,Tables, and Indexs
## Create database
### only if the databse name dosen't exist
```
CREATE DATABASE IF NOT EXISTS db_name
```
### drop the database if exist
```
DROP DATABASE IF EXIST db_name
```
### select the specified database make it as current one
```
USE db_name
```
## Create table
```
CREATE TABLE table_name
(
	column_name data_type [column_attribute] [column_level_constraint]
)
```


## Alter the columns of the table
MySQL won't allow you to change a column if that change would cause data to be lost.
### add a new column

```
ALTER TABLE [db_name.]table_name
{
	ADD column_name data_type [cloumn_attribute]
}
```
### drop a column

```
ALTER TABLE [db_name.]table_name
{
	DROP column_name
}
```
### change the length / type/default value of a column
if you don't include an existing attribute for a column, that attribute wpi;d be dropped from the column definition.
The exception are attributes that define indexes, including the `PRIMARY KEY` and `UNIQUE` attributes.

```
ALTER TABLE [db_name.]table_name
{
	MODIFY column_name data_type [column_attribute]
}
```
### change the name of a column

```
ALTER TABLE [db_name.]table_name
{
	RENAME column_name TO new_column_name
}
```
## Alter the constraints of a table
To drop a foreign key constraint, you must know its name. If you don’t know its name, you can use MySQL Workbench to look up the name.
### add a primary key constraint

```
ALTER TABLE [db_name.]table_name
ADD PRIMARY KEY (column_name)
```
### add a foreign key constraint

```
ALTER TABLE [db_name.]table_name
ADD CONSTRAINT constraint_name 
	FOREIGN KEY (column_name) REFERENCES table_name (column_name)
```
### drop a primary key constraint

```
ALTER TABLE [db_name.]table_name
DROP PRIMARY KEY
```
### drop a foreign key constraint
```
ALTER TABLE [db_name.]table_name
DROP FOREIGN KEY column_name
```
## Alter the table
When you issue a `DROP TABLE` statement, MySQL checks to see if other tables depend on the table you’re trying to delete. If they do, MySQL won’t allow the deletion. 
### rename a table
```
RENAME TABLE table_name TO new_table_name
```
### delete all data from a table
```
TRUNCATE TABLE table_name
```
### delete a table from the database
```
DROP TABLE [db_name.]table_name
```
## Work with indexes
MySQL automatically creates an index for primary key, foreign key, and unique constraints.
An index can improve performance when MySQL searches for rows in the table. If you use a column frequently, adding an index should improve the performance of the database.
### create an (unique) index based on a single column
```
CREATE UNIQUE INDEX index_name
       ON table_name (column_name)
```
### create an index based on two columns
```
CREATE INDEX index_name
       ON table_name (column_name1, column_name2)
```
### create an index that’s sorted in descending order
```
CREATE UNIQUE INDEX index_name
       ON table_name (column_name DESC)
```
### drop an index
```
DROP INDEX index_name ON table_name
```
## Script Example
```
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
## Work with character sets & collations
When a column is defined with a string type such as CHAR or VARCHAR, MySQL stores a numeric value for each character. Then, it uses a _character set_ to map the numeric values to the characters of the string.
### 3 commonly used character sets
utf8mb4 character set is the default for MySQL 8.0 and later.

Every character set has a corresponding _collation_ that determines how the characters within the set are sorted. 

### specify a character set and collation at the database level
```
CREATE DATABASE db_name CHARSET char_set_name COLLATE collate_name
```
```
ALTER DATABASE db_name CHARSET char_set_name COLLATE collate_name
```
### specify a character set and collation at table level
```
CREATE TABLE table_name
(
	column_name data_type [column attribute]
)
CHARSET char_set_name COLLATE collate_name
```
```
ALTER TABLE table_name
MODIFY column_name data_type CHARSET char_set_name COLLATE collate_name
```
## `INSERT` rows into a Table
```
INSERT INTO table_name (first_column, second_column, third_column)
VALUES(value1_1, value1_2, value1_3),
      (value2_1, value2_2, value2_3);
```
do not forget the semicolon at the end of each statement



# Retrive Data
```
SELECT select_list
FROM table_source
WHERE search_condition
ORDER BY order_by_list
LIMIT row_limit
```
The `LIMIT` clause retrieves data that is limited to a specified number of rows.  

### `CONCAT` function to join strings
```
SELECT CONCAT(column_name1,',',column_name2) AS new_name
```
### `ALL`&`DISCTINCT`
The `ALL` and `DISCTINCT` keywords specify whether or not duplicate rows are return.  
The `DISCTINCT` keyword is used to find the unique values. This technique gives us the ability to ask, 'For each x in the table, what are all the y values?' - 'For each factory, what are all the chemicals it produces?'
To use the `DISTINCT` or `ALL` keyword, code it immediately after the SELECT keyword.

### `ORDER BY`
`ASC`  
`DESC`  

### Filtering Rows with `WHERE`
The `WHERE` clause consits of one or more *Boolean expressions* that result in a true, false or null value.  
It allows you to find rows that match a specific value, a range of values, or multiple values based on criteria supplied via an operator. 

```
| Operator  | Function                          |
|-----------|-----------------------------------|
| =         | Equal to                          |
| <> or !=  | Not equal to                      |
| >         | Greater than                      |
| <         | Less than                         | 
| IN        | Match one of a set of values      |
| LIKE      | Match a pattern (case sensitive)  |
| ILIKE     | Match a pattern (case insensitive)|  

```
#### Using `LIKE` and `ILIKE` with `WHERE`
- Percent sign (`%`) A wildcard matching one or more characters  
- Underscore (`_`) A wildcard matching just one character  
For example, if trying to find the word *baker*, the following `LIKE` partterns will match it:
```
LIKE 'b%'
LIKE '%ak%'
LIKE '_aker'
LIKE 'ba_er'
```
Don’t assume that whoever typed the names of people, places, products, or other proper nouns always remembered to capitalize them.
Using a case-insensitive search `ILIKE` would help you find variations.  

# Data Type
## Characters
- `CHAR(n)`
  A fixed-length column where the character length is specified by *n*. (Nowadays, `CHAR(n)` is used infrequently.) if you insert fewer than *n* characters in any row, SQL (PostgreSQL) pads the rest of the column with spaces.
- `VARCHAR(n)`
  A variable-length column where the *maximum* length is specified by *n*. In large databases, this practice saves considerable space.  
- `TEXT`
  A variable-length column of unlimited length.
**Typically, using `VARCHAR` with an *n* value sufficient to handle outliers is a solid strategy.**

## Numbers
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
## Dates and Times
  -  `TIMESTAMP WITH TIME ZONE`: records date and time which are useful for a range of situations you might track
  -  `DATE`: records just the date
  -  `TIME`: records just the time
  -  `INTERVAL`: holds the value representing a unit of time expressed in the format *quantity unit*, e.g.: *12 days*, *8 hours*.
## Transforming Values from One Type to Another with `CAST`
The CAST() function only succeeds when the target data type can accommodate the original value. Casting an integer as text is possible, because the character types can include numbers. Casting text with letters of the alphabet as a number is not.
```
SELECT CAST(timestamp_column AS VARCHAR(10))
```
# Importing and Exporting Data
## Working with Delimited Text Files
A delimited text file contains rows of data, and each row represents one row in a table. In each row, a character separates, or delimits, each data column.  
The character that most commonly used in separating each data colimn is the comma; hence we have *comma-separated values* i.e. *CSV* file.   
Notice that a comma separates each piece of data -— first name, last name, street, town, state, and phone —- without any spaces. The commas tell the software to treat each item as a separate column, either upon import or export.  


