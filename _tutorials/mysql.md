---
name: MySQL
tags: [DB]
style: fill
color: primary
description: MySQL fundamental.
---

{%- capture list_items -%}
Access
Server Info
DB Info
Use Database
Create Database
Delete Database
Create Table
Delete Table
Rename Table
Table Attributes
Column Types
Modify Table
Table Engines
Load Databases
Queries
WHERE clause
LIMIT clause
Sorting (ORDER BY)
Agregate Functions
Grouping (GROUP BY, HAVING)
Subqueries
{%- endcapture -%}

# Access

You can acceess a MySQL DB with the following command:

```shell
mysql -u <username> -p<password> -h <host>
```

e.g. 

```shell
mysql -u sergio -p123456 -h 192.168.0.5
```

# Server Info

```sql
SELECT VERSION();

SELECT CURRENT_DATE();

STATUS;
```

# DB Info

Show existing databases:

```sql
SHOW DATABASES;
```

Show information about existing tables:

```sql
SHOW TABLES;

/* Show table columns */
DESCRIBE <table_name>;

/* Show table creation command */
SHOW CREATE TABLE <table_name>;
```

# Use Database

```sql
USE <db_name>;
```

# Create Database

```sql
CREATE DATABASE <db_name> CHARACTER SET <character_set> COLLATE <collation_name>;
```

e.g.

```sql
CREATE DATATABSE contacts CHARACTER SET utf8 COLLATE utf8_spanish_ci;
```

# Delete Database

```sql
DROP DATABASE <db_name>;
```

# Create Table

```sql
CREATE TABLE (<columns_name_type_and_attributes>) <table_options>;
```

e.g.

```sql
CREATE TABLE user(
    email VARCHAR(30) NOT NULL,
    name VARCHAR(20) NOT NULL,
    age INTEGER,
    date_time TIMESTAMP
)ENGINE=InnoDB;
```

# Delete Table

```sql
DROP TABLE <table_name>;
```

# Rename Table

```sql
RENAME TABLE <table_name> TO <new_table_name>;
```

# Table Attributes

## NOT NULL

The column does not allow null entries.

## NULL

The column allows null entries.

## DEFAULT

It allows you to set a default value and use it in case of null.

e.g.

```sql
post_code DEFAULT W2 3ES
```

## PRIMARY KEY

The primary key constraint identify unique records in a table and only one primery key is allowed per table, however you can define a compound primary key wich contains multiple columns.

How to set:

1. Next to the column name:

```sql
CREATE TABLE <table_name>(
    <column_name> VARCHAR(5) PRIMARY KEY,
    ...
);
```

2. As a separate declaration:

```sql
CREATE TABLE <table_name>(
    <column_name> VARCHAR(5),
    ...,
    PRIMARY KEY(<column_name>)
);
```

>Note: The second way is required when the primary is compound.
e.g.

```sql
CREATE TABLE contact(
    id INTEGER,
    email VARCHAR(20),
    ...,
    PRIMARY KEY(id, email)
);
```

## UNIQUE KEY

Same as `PRIMARY KEY` but it allows null entries.

## FOREIGN KEY

They are used to link two tables. A foreign key is a column in a table that points to a primary key of another table.

How to set:

1. Next to the column name:

```sql
CREATE TABLE <table_name>(
    <column_name> VARCHAR(5) REFERENCES <other_table_name>(<other_column_name>),
    ...
);
```

2. As a separate declaration:

```sql
CREATE TABLE <table_name>(
    ...,
    FOREIGN KEY(<column_name>) REFERENCES <other_table_name>(<other_column_name>)
);
```

## ON DELETE and ON UPDATE

MySQL provides some actions to automate changes from children tables which refer to a parent table:

* `NO ACTION`: not changes will be made to children tables.
* `CASCADE`: it will update or delete entries on children tables.
* `SET NULL`: the foreign key of the children tables will be set to null in case of change or deletion.

>All these operation will only affect foreign keys.

e.g. 

```sql
CREATE TABLE pet(
    code INTEGER PRIMARY KEY,
    name VARCHAR(50),
    type VARCHAR(50),
    client VARCHAR(9) REFERENCES client(id) ON DELETE CASCADE ON UPDATE SET NULL
);
```

For this particular example if one of the records is deleted from `client`, all the entries in the table `pet` with the matching client `id` will be deleted. In case of an update all the pet entries with the same client `id` will be set to 0. Alternativily you could set the foreign key as `NO ACTION`, and nothing will change in the `pet` table when changes are made on `client` table.

>Note: On delete and on update only work for with tables with store engines that support foreign keys like InnoDB.

## AUTO_INCREMENT

It allows you to generate unique value for new rows and every time a new record is added the field gets incremented.

## CHARACTER SET

MySQL support multiple charater sets to define what characters are legal in your table.

To get avaialble character sets:

```sql
SHOW CHARACTER SET;
```

The character set can be specified for at a database or table level.

```sql
CREATE DATABASE <db_name>
CHARACTER SET character_set_name;
```

```sql
CREATE TABLE <table_name>(
   ...
)
CHARACTER SET <character_set_name>
```

## CHECKSUM

It generates a number based on the table content. This could take a long time if the table is very large.

## COMMENT

Comments can be added when a table is created.

## MAX_ROWS

It is the maximum number of entries you are planning to store in a table, but is not a hard limit and only a hint.

## MIN_ROWS

It is just a hint for the engine about the memory use.

# Column Types

Type | Description | Format | Size
---------|----------|---------|---------
 `INT` | Integer | N/A | 4 bytes
 `DOUBLE` | A normal precision number | N/A | 8 bytes
 `FLOAT` | A mall precision number | N/A | 4 bytes
 `DECIMAL` | Exact decimal number | N/A | variable
 `BOOLEAN` | 0 is false and 1 true | | 1 byte
 `YEAR` | A year in four-digit format. | `YYYY` | 1 byte
 `DATE` | A date | `aaaa-mm-dd` | 3 bytes
 `TIME` | A time | `hh:mm:ss` | 3 bytes
 `DATETIME` | Date and time | `aaaa-mm-dd hh:mm:ss` | 8 bytes
 `TIMESTAMP` | Stores time as an integer which represents the time since `1970-01-01 00:00:00 UTC`. This type does not allow dates after `2038-19-01 03:14:07 UTC` | `aaaa-mm-dd hh:mm:ss` | 4 bytes
 `CHAR` | Characters | N/A | fix-length defined when stored
 `VARCHAR` | Characters | N/A | variable-length string
 `BLOB` | Binary objects like pictures | N/A | variable
 `TEXT` | Texts | N/A | variable
 `ENUM` | It is a string with a value chosen from a list of values. e.g. `color ENUM('red', 'yellow', 'green')` | N/A | 1 or 2 bytes
 `SET` | It can have zero or more values from a list of allowed values | N/A | 1, 2, 3, 4, or 8 bytes

# Modify Table

* Add column:

```sql
ALTER TABLE <table_name> ADD <new_column_name> <columns_type_and_attributes> <AFTER/FIRST>;
```

* Modify column type:

```sql
ALTER TABLE <table_name> MODIFY <column_name> <columns_type>;
```

* Remove primary key and foreign key restrictions:

```sql
ALTER TABLE <table_name> DROP PRIMARY KEY;
```

* Remove column:

```sql
ALTER TABLE <table_name> DROP <column_name>;
```

* Change column name:

```sql
ALTER TABLE <table_name> CHANGE <old_name> <new_name> <columns_type_and_attributes>;
```

# Table Engines

Set table engine:

```sql
CREATE TABLE <table_name>(
    ...
)ENGINE=<engine_name>;
```

Each DB engine has different features like table struture, therefore you have to decide which one is better for your use case.

1. **InnoDB**: Generates three files for each table, allows foreign keys, transactions, entry blocks, integrity control... and as a result the performance is lower.

2. **MyISAM**: Creates a single file for each table and other files in shared folders. The performance is higher than InnoDB since it contains fewer checks. One of the main disadvanges is that it does not allow foreign keys.

3. **MemoryEngine**: It uses volatile memory and as a result is much faster, however everything is gone after each session.

>The default engine in _Linux OS_ is _MyISAM_ and in _Windows OS_ is _InnoDB_.

# Load Databases

```sql
SOURCE path_to_file/file_name.sql
```

You can also load `sql` files from non interactive mode:

```shell
mysql -u <username> -p<password> < path_to_file/file_name.sql
```

# Queries

## SELECT expression

```sql
SELECT <column_name> FROM <table_name>;
```

To return only different values:

```sql
SELECT DISTINCT <column_name> FROM <table_name>;
```

>You can display data by chunks with the option `\G` at the end of the query.

## WHERE clause

```sql
SELECT <column_name> FROM <table_name> WHERE <condition>;
```

>You can use more than one condition by using logic operands: &&, ||...

Condition operators:

- **Numeric operators**: `+`, `-`, `*`, `/`, `%`
- **Rational operators**: `>`, `<`, `<>` (`!=`), `>=`, `<=`, `=`
- **Logical operators**: `AND`(`&&`), `OR`(`||`), `NOT`


Select rows with an assined group:

```sql
SELECT <column_name> 
FROM <table_name> 
WHERE <condition> IN (<value_1>, <value_2>, ...);
```

e.g. 

```sql
SELECT name, height, nationality 
FROM player 
WHERE team_name='Lakers' AND nationality IN ('Spain', 'Slovenia', 'Serbia');
```

To retrieve values within a range:

The `BETWEEN` expression allows you to select entries in an specific range.

```sql
SELECT <column_name> FROM <table_name> WHERE <condition> BETWEEN <lower_value> AND <upper_value>;
```

To search for a specified pattern in a column:

- `%`: any number of characters.
- `_`: single character.

e.g. 

All entry which contains _name_.

```sql
SELECT <column_name> FROM <table_name> WHERE <condition> LIKE '%name%';
```

All entries wich contains R as the first letter, S as the last letter and 

```sql
SELECT <column_name> FROM <table_name> WHERE <condition> LIKE 'R_ _ _ _ _S';
```

All entries wich contains 'o' on the second position.

```sql
SELECT <column_name> FROM <table_name> WHERE <condition> LIKE '_o%';
```

## LIMIT clause

To constrain the number of rows to return:

```sql
SELECT <column_name> FROM <table_name> LIMIT <number_of_rows>;
```

To constrain the number of rows to return with an offset.

```sql
SELECT <column_name> FROM <table_name> LIMIT <offset>, <number_of_rows>;
```

## Sorting (ORDER BY)

You can sort table rows by columns with the following sintax:

```sql
SELECT <column_name> <other_column_name>
FROM <table_name>
ORDER BY <column_name>, <other_column_name>;
```

>Note: the first specified column with have precedence when sorting.

To sort in reverse order you need to add `DESC` keyword at the end.

## Agregate Functions

Name | Description
---------|----------
`SUM(<expression>)` | Return the sum
`AVG(<expression>)` | Return the average value of the argument
`MIN(<expression>)` | Return the minimum value
`MAX(<expression>)` | Return the maximum value
`COUNT(<expression>)` | Returns a count of the number of non-NULL values

>Note: `COUNT(*)` will return the total number of rows in a table.

## Grouping (GROUP BY, HAVING)

The `GROUP BY` keyword groups table rows into a set of rows by values of columns or expressions. It will return one row for each group.

e.g. 

The following query will return the number of vehicle by brand.

```sql
 SELECT brand, COUNT(*) FROM vehicle GROUP BY brand;
```

`HAVING` clause is usually used in combination with `GROUP BY` clause to filter groups based on a specified condition. When `GROUP BY` is not specified `HAVING` behaves in the same way as `WHERE` clause. 

>Note: `HAVING` clause must come after any `GROUP BY` clause and before any `ORDER BY` clause.

>Note: you should NOT use `WHERE` clause to filter groups, since `WHERE` will do the filtering before `GROUP BY`.

e.g. 

```sql
SELECT team_name, AVG(weight) 
FROM player 
GROUP BY team_name 
HAVING AVG(weight) > 228 
ORDER BY AVG(weight);
```

## Subqueries

Subqueries are nested queries. Inner subqueries will run first and then the outer query. Subqueries can be use in combination with `WHERE` and `HAVING` clause.

Comparison operators:

- `=`: returns a single value.
- `=`, `<`, `>`, `>=`, `<=`, `<>`: to compare

>Note: You can also do subquery of another subquery, and this is called nested subqueries.

### IN

`IN`: returns values that match with an expression.

```sql
SELECT <column_name> 
FROM <table_name> 
WHERE <expression> 
IN (
    SELECT <inner_table_column_name> 
    FROM <inner_table_name> 
    WHERE <expression>
);
```

### ALL or ANY

`ALL`: it must follow a comparison operator and will return `TRUE` if all the values returned by the subquery satisfy the condition.

`ANY` or `SOME`: it must follow a comparison operator and will return `TRUE` if any of the values returned by the subquery satisfy the condition.

The following example will return the name and weight of the players who have position 'G' and whose weight is greater than any player whose position is 'C'.
e.g.

```sql
SELECT name, weight 
FROM player 
WHERE position =' G' AND weight > ANY (
    SELECT weight 
    FROM player 
    WHERE position = 'C'
);
```

### EXISTS or NOT EXISTS

It returns `TRUE`if the inner table contains any rows.

e.g.

```sql
SELECT store_type 
FROM store
WHERE EXISTS (
    SELECT * 
    FROM city_store
    WHERE city_store.store_type = store.store_type
);
```
