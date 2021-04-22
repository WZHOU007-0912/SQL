# Design Database
## Design data structure

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
Common column attributes
![截屏2021-04-21 下午9.28.05.png](https://boostnote.io/api/teams/DD9-w2C65/files/2e5badc5b664baf9dad9b6f27b704210c1b0d127b27a55105065b4b1c74a6449-%E6%88%AA%E5%B1%8F2021-04-21%20%E4%B8%8B%E5%8D%889.28.05.png)
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
