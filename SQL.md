SQL
===
###### tags: `Database`

# Basic
- SQL = Structured Query Language
- RDBMS = Relational Database Management System
    - MS SQL Server, IBM DB2, Oracle, MySQL, and Microsoft Access.
- Syntax
    - SQL keywords are NOT case sensitive
        - select is the same as SELECT
    - Semicolon
        - Some database systems require a semicolon at the end of each SQL statement.
        - Semicolon is the standard way to separate each SQL statement in database systems that allow more than one SQL statement to be executed in the same call to the server.
    - single quotes
        - SQL requires single quotes around text values (most database systems will also allow double quotes).
        - However, numeric fields should not be enclosed in quotes
        - column name, table name, and db name DO NOT NEED quotes
- table
    - row = record = entry
    - column = field

- 基本語法
```sql=
SELECT column1, column2, …, columnN 
FROM table1, table2, …, tableN
WHERE conditions 
GROUP BY column1, column2, …, columnN
HAVING search_conditions
ORDER BY column1, column2, …, columnN

```
![](https://i.imgur.com/I8fYliy.png)


> 字串跟日期需要用單引號
> 數值不需要用單引號

# MySQL

![](https://i.imgur.com/YTpoPAx.png)

- 創表格時指定 Engine
![](https://i.imgur.com/KSZP1rH.png)

# 操作的種類
## DDL
- SQL 的**資料定義**語言
- 包含建立 **Schema, VIEW, INDEX, Constraint**
## DML
- SQL 的**資料操作**語言
- 包含 **INSERT, UPDATE, DELETE**

## DCL
- SQL 的**資料控制**語言
- 即權限定義與管理
- GRANT 給予權限
- REVOKE 撤銷權限
# Important SQL Commands

- SELECT - extracts data from a database
- UPDATE - updates data in a database
- DELETE - deletes data from a database
- INSERT INTO - inserts new data into a database
- CREATE DATABASE - creates a new database
- ALTER DATABASE - modifies a database
- CREATE TABLE - creates a new table
- ALTER TABLE - modifies a table
- DROP TABLE - deletes a table
- CREATE INDEX - creates an index (search key)
- DROP INDEX - deletes an index

> 動詞 + 名詞
> 動詞 CREATE, ALTER, DROP 用在 DATABASE, TABLE, INDEX 上
> 動詞 INSERT INTO, SELECT, UPDATE, DELETE 用在 data row 上




# DDL (Data Definition Language)
## DB 
### CREATE DATABASE
```sql=
CREATE DATABASE databasename; 

-- example
REATE DATABASE testDB;
```
> 若是 databasename 跟 tablename 都不需要用 single quote 包起來

### SHOW DATABASES
- 查看現有的 DB

### DROP DATABASE
```sql=
DROP DATABASE databasename; 
```

### BACKUP DATABASE
```sql=
-- create a full back up
BACKUP DATABASE databasename
TO DISK = 'filepath'; 

-- only backs up the parts of the database that have changed since the last full database backup.
BACKUP DATABASE databasename
TO DISK = 'filepath'
WITH DIFFERENTIAL; 
```
## TABLE
### CREATE TABLE
- [data type](https://www.w3schools.com/sql/sql_datatypes.asp)
- use Create Table to copy an old table to new table
    - The new table gets the same column definitions. All columns or specific columns can be selected.
    - the new table will be filled with the existing values from the old table.
```sql=
-- create new table
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    column3 datatype,
   ....
);

-- copy of an existing table
CREATE TABLE new_table_name AS
    SELECT column1, column2,...
    FROM existing_table_name
    WHERE ....; 

-- example
CREATE TABLE Persons (
    PersonID int,
    LastName varchar(255),
    FirstName varchar(255),
    Address varchar(255),
    City varchar(255)
);

-- example2
CREATE TABLE TestTable AS
SELECT customername, contactname
FROM customers; 

```

> 如果要複製 column definition 但不要 data，可以參考先前的 SELECT...INTO 指令
> 若是 databasename 跟 tablename 都不需要用 single quote 包起來

### DROP TABLE
- DROP: 直接刪除一個 table 
- **TRUNCATE: 清空 table 裡面的資料**
```sql=
DROP TABLE table_name; 
TRUNCATE TABLE table_name; 
```

### ALTER TABLE
- add, delete, or modify columns in an existing table.
- add and drop various constraints on an existing table.

```sql=
--  新增 column
ALTER TABLE table_name
ADD column_name datatype; 

-- 刪除 column
ALTER TABLE table_name
DROP COLUMN column_name;

-- 修改 column (MySQL)
ALTER TABLE table_name
MODIFY COLUMN column_name datatype; 

-- example1
ALTER TABLE Customers
ADD Email varchar(255);

-- example2
ALTER TABLE Customers
DROP COLUMN Email;

```
> 修改 table 的 column 或 constraints
> 只有 ADD 時可以直接加上 column_name
> DROP 跟 MODIFY 都要加上 COLUMN 再寫 column_name

### Constraints
- 在 CREATE TABLE 或 ALTER TABLE 時，每個 column 都可以設定 constraint
- If there is any violation between the constraint and the data action, the action is aborted.
- Constraints 有 Column level 跟 Table level
```sql=
CREATE TABLE table_name (
    column1 datatype constraint,
    column2 datatype constraint,
    column3 datatype constraint,
    ....
);
```

- 常用 constraints
    - NOT NULL - Ensures that a column cannot have a NULL value
    - UNIQUE - Ensures that all values in a column are different
    - PRIMARY KEY - A combination of a **NOT NULL and UNIQUE**. Uniquely identifies each row in a table
    - FOREIGN KEY - Uniquely identifies a row/record in another table
    - CHECK - Ensures that all values in a column satisfies a specific condition
    - DEFAULT - Sets a default value for a column when no value is specified
    - INDEX - Used to create and retrieve data from the database very quickly

#### NOT NULL
```sql=
-- create table
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255) NOT NULL,
    Age int
); 

-- alter table
ALTER TABLE Persons
MODIFY Age int NOT NULL; 
```

#### UNIQUE
- Both the UNIQUE and PRIMARY KEY constraints provide a guarantee for uniqueness for a column or set of columns.
    - UNIQUE: can have many UNIQUE constraints per table
    - PRIMARY KEY: automatically has a UNIQUE constraint. Only one PRIMARY KEY constraint per table.

- 在 MySQL 中是要用 `UNIQUE(column_name1, column_name2)` 的形式
- 可以幫 UNIQUE constraint 取名字
    - CONSTRAINT constraint_name UNIQUE(columns)

```sql=
-- create table
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    UNIQUE (ID)
); 

-- To name a UNIQUE constraint, and to define a UNIQUE constraint on multiple columns
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    CONSTRAINT UC_Person UNIQUE (ID,LastName)
); 



-- alter table
ALTER TABLE Persons
ADD UNIQUE (ID); 

-- To name a UNIQUE constraint, and to define a UNIQUE constraint on multiple columns
ALTER TABLE Persons
ADD CONSTRAINT UC_Person UNIQUE (ID,LastName); 

-- drop a UNIQUE constraint (by constraint name)
ALTER TABLE Persons
DROP INDEX UC_Person; 
```

> MySQL 要 drop UNIQUE 的語法比較特別, 要用 `DROP INDEX constraint_name`


#### PRIMARY KEY
- The PRIMARY KEY constraint uniquely identifies each record in a table.
- Primary = UNIQUE values + NOT NULL
- A table can have only one primary key, which may consist of single or multiple fields.
- 在 MySQL 中是要用 `PRIMARY KEY(column_name1, column_name2)` 的形式

```sql=
-- create table
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    PRIMARY KEY (ID)
); 

-- To allow naming of a PRIMARY KEY constraint, and for defining a PRIMARY KEY constraint on multiple columns
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    CONSTRAINT PK_Person PRIMARY KEY (ID,LastName)
); 

-- alter table
ALTER TABLE Persons
ADD PRIMARY KEY (ID); 

-- naming
ALTER TABLE Persons
ADD CONSTRAINT PK_Person PRIMARY KEY (ID,LastName); 

-- drop 
ALTER TABLE Persons
DROP PRIMARY KEY; 
```

> If you use the ALTER TABLE statement to add a primary key, the primary key column(s) must already have been declared to not contain NULL values (when the table was first created).
> MySQL 中要 DROP PRIMARY KEY 的話不需要有 constraint_name，單純用 DROP PRIMARY KEY 就好（因為key只有一個）

#### AUTO INCREMENT
- allows a unique number to be generated automatically when a new record is inserted into a table.
    - Often this is the primary key field
    - To insert a new record into the table, we will NOT have to specify a value for the auto_increment column
    - a unique value will be added automatically
- Syntax for MySQL
    - MySQL uses the AUTO_INCREMENT keyword to perform an auto-increment feature.
    - By default, the starting value for AUTO_INCREMENT is 1, and it will increment by 1 for each new record.

```sql=
-- example, defines the "ID" column to be an auto-increment primary key field
CREATE TABLE Persons (
    ID int NOT NULL AUTO_INCREMENT,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    PRIMARY KEY (ID)
); 

-- To let the AUTO_INCREMENT sequence start with another value
ALTER TABLE Persons AUTO_INCREMENT=100; 
```


#### FOREIGN KEY
- The FOREIGN KEY constraint is used to prevent actions that would destroy links between tables.
- 在 MySQL 中是要用 `FOREIGN KEY(column_name) REFERENCE table_name(column_name)` 的形式

```sql=
-- create table
CREATE TABLE Orders (
    OrderID int NOT NULL,
    OrderNumber int NOT NULL,
    PersonID int,
    PRIMARY KEY (OrderID),
    FOREIGN KEY (PersonID) REFERENCES Persons(PersonID)
); 

-- naming
CREATE TABLE Orders (
    OrderID int NOT NULL,
    OrderNumber int NOT NULL,
    PersonID int,
    PRIMARY KEY (OrderID),
    CONSTRAINT FK_PersonOrder FOREIGN KEY (PersonID)
    REFERENCES Persons(PersonID)
); 

-- alter
ALTER TABLE Orders
ADD FOREIGN KEY (PersonID) REFERENCES Persons(PersonID); 

-- namimg
ALTER TABLE Orders
ADD CONSTRAINT FK_PersonOrder
FOREIGN KEY (PersonID) REFERENCES Persons(PersonID);

-- drop
ALTER TABLE Orders
DROP FOREIGN KEY constraint_name; 
```

- Foreign Key 的種類
![](https://i.imgur.com/Slc2cSM.png)

![](https://i.imgur.com/lkTXZo2.png)






> 語法 FOREIGN KEY (column_name) REFERENCE table_name(column_name)


#### CHECK
- 可以檢查輸入
- The CHECK constraint is used to limit the value range that can be placed in a column.
    - a CHECK constraint on a single column it allows only certain values for this column.
    - a CHECK constraint on a table it can limit the values in certain columns based on values in other columns in the row.

- 在 MySQL 中是要用 `CHECK(condition)` 的形式

```sql=
-- create table
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    CHECK (Age>=18)
); 

-- naming
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    City varchar(255),
    CONSTRAINT CHK_Person CHECK (Age>=18 AND City='Sandnes')
); 

-- alter
ALTER TABLE Persons
ADD CHECK (Age>=18); 

-- naming
ALTER TABLE Persons
ADD CONSTRAINT CHK_PersonAge CHECK (Age>=18 AND City='Sandnes'); 

-- drop
ALTER TABLE Persons
DROP CHECK CHK_PersonAge; 
```

#### DEFALUT
- provide a default value for a column
```sql=
-- create
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    City varchar(255) DEFAULT 'Sandnes'
); 

-- use system values as defalut
CREATE TABLE Orders (
    ID int NOT NULL,
    OrderNumber int NOT NULL,
    OrderDate date DEFAULT GETDATE()
); 

-- alter
ALTER TABLE Persons
ALTER City SET DEFAULT 'Sandnes'; 

-- drop
ALTER TABLE Persons
ALTER City DROP DEFAULT; 
```
#### 對 CONSTRAINT 的操作統整
- 如果沒有要幫 constraint 命名，則在 MySQL 中語法是
    - UNIQUE(column_name1, column_name2)
    - PRIMARY KEY(column_name1, column_name2)
    - FOREIGN KEY(column_name) REFERENCES table_name(column_name)
    - 然而若沒有命名，要 DROP constraint 時會比較麻煩
- 如果要幫 constraint 命名
    - 則在上面的指令前面加上 `CONSTRAINT constraint_name`

### CREATE INDEX
- Indexes are used to retrieve data from the database very fast.
    - The users cannot see the indexes, they are just used to speed up searches/queries.
    - Updating a table with indexes takes more time than updating a table without (because the indexes also need an update)
    - 是 B-Tree 或其變種: 優點是可以減少 I/O
- 可以分為允許 Duplicate values 的 INDEX 及不允許的 INDEX
- 不同 RDBMS 的語法不一樣
```sql=
-- Duplicate values are allowed
CREATE INDEX index_name
ON table_name (column1, column2, ...); 

-- Duplicate values are not allowed
CREATE UNIQUE INDEX index_name
ON table_name (column1, column2, ...); 

-- Drop INDEX
ALTER TABLE table_name
DROP INDEX index_name; 

-- example, create an index on a combination of columns
CREATE INDEX idx_pname
ON Persons (LastName, FirstName); 
```

> Only create indexes on columns that will be frequently searched against.

### CREATE VIEW
- view is a **virtual table** based on the result-set of an SQL statement.
    - A view contains rows and columns, just like a real table.
    - The fields in a view are fields from one or more real tables in the database.
    - query VIEW 的方法就跟之前 query 正常 table 時一樣

- You can add SQL functions, WHERE, and JOIN statements to a view and present the data as if the data were coming from one single table.

- **A view always shows up-to-date data!**
    -  The database engine recreates the data, using the view's SQL statement, every time a user queries a view.
    -  因此 view 不是某個狀態下的快照，是由現有 table 整理而成的捷徑
```sql=
-- create view
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition; 

-- update view
CREATE OR REPLACE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition; 

-- drop view
DROP VIEW view_name; 

-- example, create view
CREATE VIEW [Products Above Average Price] AS
SELECT ProductName, Price
FROM Products
WHERE Price > (SELECT AVG(Price) FROM Products); 

-- example query the view above
SELECT * FROM [Products Above Average Price]; 

-- example, update view
CREATE OR REPLACE VIEW [Brazil Customers] AS
SELECT CustomerName, ContactName, City
FROM Customers
WHERE Country = "Brazil"; 

-- example, drop view
DROP VIEW [Brazil Customers]; 
```

> View 不是某個狀態下的快照，是由現有 table 整理而成的捷徑
> update view 的指令比較特別，要用 `CREATE OT REPLACE VIEW`
> 

### 資料表複製
#### SELECT INTO

- The SELECT INTO statement **copies data from one table into a new table (or in external db).**
- The new table will be created with the column-names and types as defined in the old table.
    - You can create new column names using the AS clause.
- Tip
    - **SELECT INTO can also be used to create a new, empty table using the schema of another.**
    - Just add a WHERE clause that causes the query to return no data (1=0)
```sql=
-- Copy all columns into a new table:
SELECT *
INTO newtable [IN externaldb]
FROM oldtable
WHERE condition; 

-- Copy only some columns into a new table
SELECT column1, column2, column3, ...
INTO newtable [IN externaldb]
FROM oldtable
WHERE condition; 

-- create a new, empty table using the schema of another
SELECT * INTO newtable
FROM oldtable
WHERE 1 = 0; 

-- example, as backup
-- The following SQL statement uses the IN clause to copy the table into a new table in another database
SELECT * INTO CustomersBackup2017 IN 'Backup.mdb'
FROM Customers; 

-- copies data from more than one table into a new table
SELECT Customers.CustomerName, Orders.OrderID
INTO CustomersOrderBackup2017
FROM Customers
LEFT JOIN Orders ON Customers.CustomerID = Orders.CustomerID; 

```
> Can use in BackUp, 
> Can create a new, empty table using the schema of another
> 複製資料到一個全新的資料庫


#### INSERT INTO SELECT
- The INSERT INTO SELECT statement copies data from one table and inserts it into another table.
    - INSERT INTO SELECT requires that data types in source and target tables match
    - The existing records in the target table are unaffected

```sql=
-- Copy all columns from one table to another table
INSERT INTO table2
SELECT * FROM table1
WHERE condition; 

-- Copy only some columns from one table into another table
INSERT INTO table2 (column1, column2, column3, ...)
SELECT column1, column2, column3, ...
FROM table1
WHERE condition; 

-- example, The following SQL statement copies only the German suppliers into "Customers"
INSERT INTO Customers (CustomerName, City, Country)
SELECT SupplierName, City, Country FROM Suppliers
WHERE Country='Germany';
```
> INSERT INTO 會寫在 SELECT 前面，極少數的語法




# DML (Data Manipulation Language)
## INSERT INTO...VALUES
- Two ways
    - specifies both the column names and the values to be inserted
    - If you are adding values for all the columns of the table, you do not need to specify the column names in the SQL query. 
        - However, **make sure the order of the values is in the same order as the columns in the table.**

```sql=
-- common -1
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...); 

-- common -2
INSERT INTO table_name
VALUES (value1, value2, value3, ...); 
```

> 使用 INSERT INTO 時， values 如果是字串要有括號包覆，數字則不用

## UPDATE...SET...WHERE

- **If you omit the WHERE clause, all records in the table will be updated!**

```sql=
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition; 
```

## DELETE FROM...WHERE
- **If you omit the WHERE clause, all records in the table will be deleted!**


```sql=
DELETE FROM table_name WHERE condition;

-- Delete all records in a table
DELETE FROM table_name;
```
> DELETE FROM 只需要 table_name

# DQL (Data Query Language)
**每個指令都需要指定 table name 跟 column name**

## SELECT

```sql=
-- common
SELECT column1, column2, ...
FROM table_name; 

-- select all
SELECT * FROM table_name;

-- several table
SELECT table1.column1, table2.column2, ...
FROM table1, table2

```
> 如果 column 分別來自不同 table，則每次提及這個 column 時都要使用 table.column。可以使用 AS 來縮短這類語句

### SELECT DISTINCT
- 選出不重複的值
- 可以搭配 `COUNT`
```sql=
-- The SELECT DISTINCT statement is used to return only distinct (different) values.
SELECT DISTINCT column1, column2, ...
FROM table_name;

-- count of different values (not supported in Microsoft Access databases)
SELECT COUNT(DISTINCT column) FROM table_name;

-- count of different values (Microsoft Access databases)
SELECT Count(*) AS DistinctCountries
FROM (SELECT DISTINCT Country FROM Customers);
```

### SELECT ... AS
- give a **table, or a column in a table**, a temporary name.
- alias only exists for the duration of the query.
- It requires **double quotation marks or square brackets** if the alias name contains spaces
- aliases can make SQL shorter (often used in table name)

```sql=
-- Column 
SELECT column_name AS alias_name
FROM table_name;

-- Table
SELECT column_name(s)
FROM table_name AS alias_name;

-- example
SELECT CustomerName AS Customer, ContactName AS [Contact Person]
FROM Customers;

-- example2, creates an alias named "Address" that combine four columns
SELECT CustomerName, CONCAT(Address,', ',PostalCode,', ',City,', ',Country) AS Address
FROM Customers; 

-- example3
SELECT o.OrderID, o.OrderDate, c.CustomerName
FROM Customers AS c, Orders AS o
WHERE c.CustomerName="Around the Horn" AND c.CustomerID=o.CustomerID;
```

> AS 出現在 SELECT 語句上
> AS 可以用在 table 或 column 上
> 如果 alias 包含空格的話，就要用 **雙引號或是[]** 括起來
> 也時常搭配 CONCAT 來使用

### SELECT TOP, LIMIT or ROWNUM Clause
- is used to specify the number of records to return.
- **is useful on large tables with thousands of records.** 
    - Returning a large number of records can impact on performance.
- 不同 RDBMS 支援不同的語法
    - SQL Server / MS Access Syntax 使用 SELECT TOP
    - **MySQL 使用 LIMIT**
    - Oracle 使用 ROWNUM

```sql=
-- MySQL
SELECT column_name(s)
FROM table_name
WHERE condition
LIMIT number; 

-- Oracle
SELECT column_name(s)
FROM table_name
WHERE ROWNUM <= number; 

-- SQL Server / MS Access Syntax
SELECT TOP number|percent column_name(s)
FROM table_name
WHERE condition; 

-- 以上三種是等價的
```

## WHERE
- The WHERE clause is used to extract only those records that fulfill a specified condition.

- The WHERE clause is not only used in SELECT statement, it is also used in UPDATE, DELETE statement, etc.
```sql=
-- common
SELECT column1, column2, ...
FROM table_name
WHERE condition; 

-- example, with Arithmetic Operators
SELECT c_no, title, credits + 1 AS NewCredits 
FROM Courses
WHERE credits < 4

```
![](https://i.imgur.com/uzk104W.png)

- WHERE 子句的邏輯運算子包含
	- LIKE包含子字串運算子
	- IS NULL運算子
	- EXISTS運算子
	- BETWEEN/AND範圍運算子
	- IN運算子
	- AND, OR, NOT運算子
	- ![](https://i.imgur.com/OfT1vNh.png)

### Subqueries
- 子查詢是位在SQL指令的括號之中
- EXISTS 和 IN 都可以使用在子查詢
    - 如果子查詢取得多筆記錄，在主查詢需要使用IN邏輯運算子。
- 通常子查詢的SELECT指令只會取得單一欄位值，以便與主查詢的欄位進行比較運算
- 如果需要排序，主查詢可以使用ORDER BY子句，子查詢不可以使用ORDER BY子句，不過可以使用GROUP BY子句來替代。
- BETWEEN/AND邏輯運算子不能使用在主查詢，但是可以在子查詢使用。

### LIKE
- The LIKE operator is **used in a WHERE clause** to search for a specified pattern in a column.
- two wildcards that most use
    - % - The percent sign represents zero, one, or multiple characters
    - _ - The underscore represents a single character

```sql=
SELECT column1, column2, ...
FROM table_name
WHERE columnN LIKE pattern; 
```

- example
    - WHERE CustomerName LIKE 'a%' (Finds any values that start with "a")
    - WHERE CustomerName LIKE '%a' (Finds any values that end with "a")
    - WHERE CustomerName LIKE '%or%' (Finds any values that have "or" in any position)
    - WHERE CustomerName LIKE '_r%' (Finds any values that have "r" in the second position)
    - WHERE CustomerName LIKE 'a_%_%' (**Finds any values that start with "a" and are at least 3 characters in length**)
    - WHERE ContactName LIKE 'a%o' (Finds any values that start with "a" and ends with "o")
    - WHERE CustomerName NOT LIKE 'a%' (Finds any value that **does NOT start with "a"**)

> LIKE 語句需要搭配 WHERE 使用
> pattern 要用 single quote 括號起來



####  Wildcard Characters
- 依據不同 RDBMS 會有不同 wildcards

![](https://i.imgur.com/tuD99eD.png)


- example
    - WHERE CustomerName LIKE '[bsp]%' (Finds any values that start with "b", "s", or "p")
    - WHERE CustomerName NOT LIKE '[bsp]%' (Finds any values that NOT start with "b", "s", or "p")
    - WHERE CustomerName LIKE '[a-c]%' (Finds any values that start with "a", "b", or "c")

### IFNULL()
- 不同 RDBMS 有不同 function
    - MySQL: IFNULL() or COALESCE()
    - SQL Server: ISNULL()
    - MS Access: IsNull()
    - Oracle: NVL()

- 以 MySQL 為例
    - Look at the following SELECT statement. Suppose that the "UnitsOnOrder" column is optional, and may contain NULL values.

```sql=
SELECT ProductName, UnitPrice * (UnitsInStock + UnitsOnOrder)
FROM Products; 
```
    - Solution, 如果 UnitsOnOrder 是 NULL 則回傳 0
```sql=
SELECT ProductName, UnitPrice * (UnitsInStock + IFNULL(UnitsOnOrder, 0))
FROM Products 
```

### IS NULL
- A NULL value is different from a zero value or a field that contains spaces
- It is not possible to test for NULL values with comparison operators, such as =, <, or <>.
- **need `IS NULL` and `IS NOT NULL` operators instead**

```sql=
-- IS NULL
SELECT column_names
FROM table_name
WHERE column_name IS NULL; 

-- IS NOT NULL
SELECT column_names
FROM table_name
WHERE column_name IS NOT NULL; 
```
> 需要搭配 WHERE 一起使用
> Always use IS NULL to look for NULL values.
> 

### EXISTS
- The EXISTS operator is used to test for the existence of any record in a subquery.
    - **returns true** if the subquery returns one or more records.
- 也可以搭配 IF 使用，例如只有在資料表已存在的狀況下才刪除該表格
```sql=
SELECT column_name(s)
FROM table_name
WHERE EXISTS
(SELECT column_name FROM table_name WHERE condition); 

-- example
SELECT SupplierName
FROM Suppliers
WHERE EXISTS (SELECT ProductName FROM Products WHERE SupplierId = Suppliers.supplierId AND Price < 20); 

-- example2, if exists
DROP TABLE IF EXISTS table_name;
```

> WHERE EXISTS(condition)
> 也常搭配 IF 使用

### BETWEEN...AND

- The BETWEEN operator selects values within a given range
- The values can be **numbers, text, or dates.**
    - 其中如果是 text values 的話
        - 會直接按照**所有字母順序**排列，不需要特別 ORDER BY
        - 意思是 BETWEEN 'Car' AND 'Egg' 跟 BETWEEN 'Cbr' AND 'Egg' 是不一樣的，會差在那些 Ca 開頭的字
        - 英文字母的**大小寫沒有差**
- **begin and end values are included.**

```sql=
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2; 

-- example
SELECT * FROM Products
WHERE (Price BETWEEN 10 AND 20)
AND NOT CategoryID IN (1,2,3);

-- example2
SELECT * FROM Products
WHERE ProductName BETWEEN 'Carnarvon Tigers' AND 'Mozzarella di Giovanni'
ORDER BY ProductName;

-- example
SELECT * FROM Orders
WHERE OrderDate BETWEEN '1996-07-01' AND '1996-07-31';
```


> BETWEEN 語句需要搭配 WHERE 使用
> 可以用在 number, text, date 上
> date 一樣用 single quote 就好
> 可以搭配 IN, ORDER BY 使用. 其中 ORDER BY 常搭配 text values 使用
> 

### IN

- The IN operator is a shorthand for multiple OR conditions.
```sql=
-- basic
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...); 

-- advanced (不需要寫死 value)
ELECT column_name(s)
FROM table_name
WHERE column_name IN (SELECT STATEMENT); 

-- example
SELECT * FROM Customers
WHERE Country IN (SELECT Country FROM Suppliers);
```

> IN 語句需要搭配 WHERE 使用
> IN 是好幾個 OR 的縮短寫法

### AND, OR, NOT
- The WHERE clause can be combined with AND, OR, and NOT operators.
    - AND and OR operators are used to filter records based on more than one condition
    - The NOT operator displays a record if the condition(s) is NOT TRUE.

- NOT 可以接： NOT LIKE, NOT BETWEEN, NOT IN 等
```sql=
-- logic operators

-- AND
SELECT column1, column2, ...
FROM table_name
WHERE condition1 AND condition2 AND condition3 ...; 

-- OR
SELECT column1, column2, ...
FROM table_name
WHERE condition1 OR condition2 OR condition3 ...; 

-- NOT
SELECT column1, column2, ...
FROM table_name
WHERE NOT condition; 

-- Combine example
SELECT * FROM Customers
WHERE Country='Germany' AND (City='Berlin' OR City='München'); 

-- example2 
SELECT * FROM Customers
WHERE NOT Country='Germany' AND NOT Country='USA'; 

```

> NOT 的位置跟 AND, OR 不一樣，是馬上接在 WHERE 後面




## Aggregate Functions
### MIN() and MAX()
- 用在**數值**的 field 上
- 常跟 `AS` 搭配，取一個別名
    - 如果沒有取別名，顯示的欄位可能會無法預期 (?)

```sql=
-- min
SELECT MIN(column_name)
FROM table_name
WHERE condition; 

-- max
SELECT MAX(column_name)
FROM table_name
WHERE condition; 

-- 搭配 AS example
SELECT MIN(Price) AS SmallestPrice
FROM Products;
```
### COUNT(), AVG(), SUM()

- The COUNT() function returns the number of rows that matches a specified criteria.

- The AVG() function returns the average value of a numeric column.

- The SUM() function returns the total sum of a numeric column.

```sql=
-- count
SELECT COUNT(column_name)
FROM table_name
WHERE condition; 

-- average
SELECT AVG(column_name)
FROM table_name
WHERE condition; 

-- sum
SELECT SUM(column_name)
FROM table_name
WHERE condition; 
```

> 跟著 SELECT 的 column 使用

## 群組與排序
### GROUP BY
- The GROUP BY statement is often used with aggregate functions (COUNT, MAX, MIN, SUM, AVG) to group the result-set by one or more columns.

```sql=
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
ORDER BY column_name(s); 

-- example, **對於 Country 分別計數有幾個 Customer, 並依據大到小排序，常用**
-- 下述如果 SELECT 的時候有選擇 Country 這個 field, 則後面一定要指定 GROUP BY, 不然 syntax error
SELECT COUNT(CustomerID), Country
FROM Customers
GROUP BY Country
ORDER BY COUNT(CustomerID) DESC;

-- example2
SELECT Shippers.ShipperName, COUNT(Orders.OrderID) AS NumberOfOrders FROM Orders
LEFT JOIN Shippers ON Orders.ShipperID = Shippers.ShipperID
GROUP BY ShipperName;
```

> 常搭配 COUNT, MAX, MIN, SUM, AVG 等 function 使用
> 
### HAVING
- The HAVING clause was added to SQL because the **WHERE keyword could not be used with aggregate functions.**

```sql=
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
HAVING condition
ORDER BY column_name(s);

-- example
-- The following SQL statement lists the number of customers in each country, sorted high to low (Only include countries with more than 5 customers)
SELECT COUNT(CustomerID), Country
FROM Customers
GROUP BY Country
HAVING COUNT(CustomerID) > 5
ORDER BY COUNT(CustomerID) DESC;


-- example
-- The following SQL statement lists if the employees "Davolio" or "Fuller" have registered more than 25 orders
SELECT Employees.LastName, COUNT(Orders.OrderID) AS NumberOfOrders
FROM Orders
INNER JOIN Employees ON Orders.EmployeeID = Employees.EmployeeID
WHERE LastName = 'Davolio' OR LastName = 'Fuller'
GROUP BY LastName
HAVING COUNT(Orders.OrderID) > 25;

```

> HAVING 跟 WHERE 很像，但 WHERE 不能用在 aggregate function (COUNT, MAX, MIN, SUM, AVG, COUNT), 因此要用 HAVING
> HAVING 後面接著的 condition 一定會包含 aggregate function
> 
> **HAVING 是針對 GROUP 中下的搜尋條件**


### ORDER BY
- order
    - ascending order by default
    - To sort the records in descending order, use the DESC keyword.
 
```sql=
-- common, ORDER BY Several Columns
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2, ... ASC|DESC; 

-- DESC example
SELECT * FROM Customers
ORDER BY Country DESC; 

-- ORDER BY Several Columns example
SELECT * FROM Customers
ORDER BY Country, CustomerName; 

-- example2
SELECT * FROM Customers
ORDER BY Country ASC, CustomerName DESC; 

```

## JOIN
- is used to combine rows from two or more tables, based on a **related column** between them.
- syntax SELECT...FROM table1 INNER/LEFT/RIGHT/FULL JOIN table2 ON condition
- 接下來的討論中，**跟在 FROM 語句後面的是 table1, JOIN 語句後面的是 table2**


![](https://i.imgur.com/cOe9yZY.png)

> JOIN 是 SELECT FROM 語句的延伸
> JOIN 的對象是 table
> In some databases LEFT JOIN is called LEFT OUTER JOIN.
> In some databases RIGHT JOIN is called RIGHT OUTER JOIN.
> FULL OUTER JOIN can potentially return very large result-sets
### INNER JOIN
- The INNER JOIN keyword selects records that have matching values in both tables.

```sql=
SELECT column_name(s)
FROM table1 INNER JOIN table2
ON table1.column_name = table2.column_name;

-- example, Join three table and more
-- The following SQL statement selects all orders with customer and shipper information
SELECT Orders.OrderID, Customers.CustomerName, Shippers.ShipperName
FROM ((Orders
INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID)
INNER JOIN Shippers ON Orders.ShipperID = Shippers.ShipperID); 
```



### LEFT JOIN
- The LEFT JOIN keyword returns all records from the left table (table1), and the matched records from the right table (table2). 
- The result is NULL from the right side, if there is no match.

```sql=
SELECT column_name(s)
FROM table1 LEFT JOIN table2
ON table1.column_name = table2.column_name;

-- example
SELECT Customers.CustomerName, Orders.OrderID
FROM Customers
LEFT JOIN Orders ON Customers.CustomerID = Orders.CustomerID
ORDER BY Customers.CustomerName;
```
> 會回傳所有在 FROM 之後的 table (table1), 加上符合條件的在 LEFT JOIN 之後的 table (table2)
### RIGHT JOIN
- The RIGHT JOIN keyword returns all records from the right table (table2), and the matched records from the left table (table1).
- The result is NULL from the left side, when there is no match.

```sql=
SELECT column_name(s)
FROM table1
RIGHT JOIN table2
ON table1.column_name = table2.column_name;
```
> 會回傳所有在 RIGHT JOIN 之後的 table (table2), 加上符合條件的在 FROM 之後的 table (table1)
### FULL OUTER JOIN
- MySQL 中沒有 FULL OUTER JOIN
    - 可以通過 left join union right join 達成
```sql=
SELECT * FROM t1
LEFT JOIN t2 ON t1.id = t2.id
UNION
SELECT * FROM t1
RIGHT JOIN t2 ON t1.id = t2.id
```
- The FULL OUTER JOIN keyword return all records when there is a match in either left (table1) or right (table2) table records.
- 就會回傳兩（多）個 table 中的所有資料，只是會依照 ON 的條件 join 成同一個 row

```sql=
SELECT column_name(s)
FROM table1
FULL OUTER JOIN table2
ON table1.column_name = table2.column_name; 
```

## 集合運算查詢 Statement
- 包含 聯集 UNION, 交集 INTERSECT, 差集 EXCEPT

### UNION
- The UNION operator is used to **combine the result-set of two or more SELECT statements**.
    - Each SELECT statement within UNION **must have the same number of columns**
    - The columns must also **have similar data types**
    - The columns in each SELECT statement must also **be in the same order**
    - The UNION operator selects only distinct values by default. To allow duplicate values, use **UNION ALL**

- The column names in the result-set are usually equal to the column names in the first SELECT statement in the UNION.
```sql=
-- common
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2; 

-- UNION ALL
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;

-- example
SELECT City, Country FROM Customers
WHERE Country='Germany'
UNION
SELECT City, Country FROM Suppliers
WHERE Country='Germany'
ORDER BY City;

-- example2
SELECT 'Customer' As Type, ContactName, City, Country
FROM Customers
UNION
SELECT 'Supplier', ContactName, City, Country
FROM Suppliers;
```

> UNION 搭配 WHERE 的話，要分別**兩個**敘述都加
> 
### EXCEPT
- MySQL 不支援 EXCEPT
    - 可以透過 NOT IN 實現，但效率差
    - 可以透過 LEFT JOIN + WHERE 實現，如下要找出 table_1 有但 table_2 中沒有的數據
```sql=
SELECT
  DISTINCT union_tab_1.*
FROM
  union_tab_1 LEFT JOIN union_tab_2
    ON (union_tab_1.id = union_tab_2.id
        AND union_tab_1.val = union_tab_2.val)
WHERE
  union_tab_2.id  IS  NULL;
```

## 其他邏輯 Statement
### ANY and ALL
- The ANY and ALL operators are used with a WHERE or HAVING clause.
    - ANY operator returns true if any of the subquery values meet the condition.
    - ALL operator returns true if all of the subquery values meet the condition.
- The operator must be a **standard comparison operator**
    - =, !=, >, >=, <, or <=
```sql=
-- ANY
SELECT column_name(s)
FROM table_name
WHERE column_name operator ANY
(SELECT column_name FROM table_name WHERE condition); 

-- ALL
SELECT column_name(s)
FROM table_name
WHERE column_name operator ALL
(SELECT column_name FROM table_name WHERE condition); 

-- example
SELECT ProductName
FROM Products
WHERE ProductID = ANY (SELECT ProductID FROM OrderDetails WHERE Quantity > 99); 
```

> WHERE coulumn operator ANY/ALL (condition)


### CASE...END
- The CASE statement goes through conditions and return a value when the first condition is met (like an IF-THEN-ELSE statement)
- So, once a condition is true, it will stop reading and return the result. If no conditions are true, it returns the value in the ELSE clause. 
    - If there is no ELSE part and no conditions are true, it returns NULL.

```sql=
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    WHEN conditionN THEN resultN
    ELSE result
END; 

-- example
-- 以下例子除了會回傳 OrderID, Quantity 兩個 field 以外，還會多一個 QuantityText 裡面是文字
SELECT OrderID, Quantity,
CASE
    WHEN Quantity > 30 THEN "The quantity is greater than 30"
    WHEN Quantity = 30 THEN "The quantity is 30"
    ELSE "The quantity is under 30"
END AS QuantityText
FROM OrderDetails; 

-- The following SQL will order the customers by City. However, if City is NULL, then order by Country
SELECT CustomerName, City, Country
FROM Customers
ORDER BY
(CASE
    WHEN City IS NULL THEN Country
    ELSE City
END); 
```
> CASE 開頭要用 END 結束，需要時要用括號包起來


# 對 DATA 的操作統整
```sql=
SELECT column_name(s)
FROM table_name
(LEFT/RIGHT/INNER/FULL OUTER) JOIN table_name
ON join_condition
WHERE normal_condition
GROUP BY column_name(s)
HAVING aggregate_condition
ORDER BY column_name(s);
```


# DataTypes
![](https://i.imgur.com/hR85tya.png)

![](https://i.imgur.com/eJh1fFt.png)

![](https://i.imgur.com/IC6iqC4.png)

![](https://i.imgur.com/N8bW8Hr.png)

## Dates
- MySQL 的 Date 包含 (按照**年-月-日**的順序)
    - DATE - format YYYY-MM-DD
    - DATETIME - format: YYYY-MM-DD HH:MI:SS
    - TIMESTAMP - format: YYYY-MM-DD HH:MI:SS
    - YEAR - format YYYY or YY

- 如果在 SQL Server 中
    - TIMESTAMP - format: a unique number

- Date 的比較
    - You can compare two dates easily **if there is no time component involved!**
        - 在 DATETIME 的欄位中，如果搜尋時只帶 DATE 而不包含 TIME，則搜尋不到結果
    - To keep your queries simple and easy to maintain, do not allow time components in your dates!

```sql=
-- example
-- 如果一個欄位 (OrderDate) 中只包含 DATE，則搜尋很容易，且結果是對的
-- 但如果 OrderDate 改成 DATETIME，則以下 query 只會返回 NULL，因為這個 query 沒有帶 time 的部分！
 SELECT * FROM Orders WHERE OrderDate='2008-11-11'
 

```
> The most difficult part when working with dates is to be sure that the format of the date you are trying to insert, matches the format of the date column in the database.

> To keep your queries simple and easy to maintain, do not allow time components in your dates!

> 當只操作 date 的部分時可能還容易，但如果加入了 time 的部分就會變得很複雜
> 


# Stored Procedures
- Procedure: 可以儲存成一個像 function 的一段 code, 方便重複執行
- 可以傳入參數：使用 @parameter_name
    - Setting up multiple parameters is very easy. Just list each parameter and the data type separated by a comma.
    - **Need to decalre parameters before `AS` statement**
    - **Pass in argument when execute**
```sql=
-- stored procedure
CREATE PROCEDURE procedure_name
AS
sql_statement
GO; 

-- Execute
EXEC procedure_name; 
```

- 範例 1
```sql=
-- The following SQL statement creates a stored procedure named "SelectAllCustomers" that selects all records from the "Customers" table
CREATE PROCEDURE SelectAllCustomers
AS
SELECT * FROM Customers
GO;

-- execute
EXEC SelectAllCustomers;
```

- 範例 2 (傳入參數)
```sql=
-- procedure
CREATE PROCEDURE SelectAllCustomers @City nvarchar(30), @PostalCode nvarchar(10)
AS
SELECT * FROM Customers WHERE City = @City AND PostalCode = @PostalCode
GO;

-- execute
EXEC SelectAllCustomers City = "London", PostalCode = "WA1 1DP";
```

> 創造 Procedure 由 CREATE PROCEDURE... AS...GO 組成
> 如果要定義參數，需要在 sql_statement  之前先指定好名稱跟 type, 並在 EXEC 時傳入參數的值
> 執行只需要有 EXEC 指令




# Transaction 交易管理
- 能確保多個 SQL 指令，全部執行成功，或全部不執行
- MySQL 的 `InnoDB` 有支援 Transaction
- 使用 Transaction 可以適時提高效率
    - 例如要 insert 1000 筆資料，如果沒有用 transaction，則每一筆 insert 都會 commit 一次
    - 如果有用 transaction，就可以 1000 筆一起 commit，效率會差很多
 - 交易功能4個特性 (ACID)
     - Atomicity (原子性、不可分割)：交易內的 SQL 指令，不管在任何情況，都只能是全部執行完成，或全部不執行。若是發生無法全部執行完成的狀況，則會回滾(rollback)到完全沒執行時的狀態。
     - Consistency (一致性)：交易完成後，必須維持資料的完整性。所有資料必須符合預設的驗證規則、外鍵限制...等。
     - Isolation (隔離性)：多個交易可以獨立、同時執行，不會互相干擾。這一點跟後面會提到的「隔離層級」有關。
     - Durability (持久性)：交易完成後，異動結果須完整的保留。

- 開始進入交易模式
    - SQL 指令：START TRANSACTION 或 BEGIN
- 結束交易模式
    - 交易完成：使用 COMMIT 儲存所有變動，並結束交易。
    - 交易過程異常：使用 ROLLBACK 回滾，取消交易，還原到未進行交易的狀態。(若交易過程連線中斷，沒 COMMIT 提交的變更，亦會如同執行 ROLLBACK 取消交易)
- 儲存點 (SAVEPOINT)
	- 交易過程中，可標示多個不同的儲存點，有需要時可 ROLLBACK 到某個儲存點。
	- 建立儲存點：SAVEPOINT 名稱
	- 刪除儲存點：RELEASE SAVEPOINT 名稱
	- ROLLBACK 到某個儲存點：ROLLBACK TO SAVEPOINT 名稱
	- 如果建立新儲存點時，已有同名稱的舊儲存點，舊儲存點將被刪除，並建立新的儲存點。
	- 官網說明：http://dev.mysql.com/doc/refman/5.7/en/savepoint.html

- AUTOCOMMIT 自動提交設定

	- AUTOCOMMIT 的設定值，預設一般都是 1
	- 查詢目前 AUTOCOMMIT 的設定值：SELECT @@AUTOCOMMIT
	- 將 AUTOCOMMIT 改為 0 時 ( SET AUTOCOMMIT=0 )，就算沒使用 START TRANSACTION 或 BEGIN ，整個連線執行的 SQL 指令，都會等到下達 COMMIT 提交後，才會真正儲存變更。也就是當 AUTOCOMMIT=0 時，跟在交易模式下相同。


![](https://i.imgur.com/idcaHpW.png)
![](https://i.imgur.com/Dj28SLE.png)
![](https://i.imgur.com/uDWQpBX.png)




> 使用 Transaction 可以確保執行的完整
> 
> 使用 Transaction 可以適時提高效率

# TRIGGER
- 在指令情況下時會自動觸發的函示
    - 就跟 EventListener 一樣
- 也可以拿來當作**預防檢查**
    - 例如我們不該 update 某個欄位值為 FALSE 的 row
    - 因此**在 update 之前可以先用 trigger 去檢查是否合法**

- 例如
    - 當新增一筆訂單時，就自動 trigger 去更新該 customer 的 last_order_id 這個欄位
    - 當某資料更新時，**就自動 trigger 去更新 Timestamps 這個欄位**
    - **每當 DB 某些 table 變更時，就寫一筆 Log**

```sql=
CREATE TRIGGER trigger_name trigger_time trigger_event
ON tbl_name FOR EACH ROW BEGIN trigger_body END;

-- example
CREATE TRIGGER tt_upd AFTER UPDATE ON aa
FOR EACH ROW
BEGIN
UPDATE test.bb
SET test.bb.bb_data=(NEW.aa_data1+NEW.aa_data2)
WHERE test.bb.aa_id = NEW.aa_id;
END;
```
# SQL Injection
- a code injection technique
- is one of the most common web hacking techniques

