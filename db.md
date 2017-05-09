### MSSQL Cheat Sheet

##### SQL Server Data Types
###### Exact Numerics
 - bit
 - decimal
 - tinyint
 - money
 - small
 - int
 - numeric
 - bigint
###### Approximate Numerics
 - float
 - real
###### Date and Time
 - smalldatetime
 - timestamp
 - datetime
###### Strings
 - char
 - text
 - varchar
###### Unicode Strings
 - nchar
 - ntext
 - nvarchar
###### Binary Strings
 - binary
 - image
 - varbinary
###### Miscellaneous
 - cursor
 - table
 - sql_variant
 - xml

#### SQL Server Type Conversion
 - CAST (expression AS datatype)
 - CONVERT (datatype, expression)

####SQL Server Table Functions
 - ALTER
 - DROP
 - CREATE
 - TRUNCATE
#### SQL Server Grouping (Aggregate) Functions
 - AVG <- *Returns the average of the values in a group. Null values are ignored.*
 - MAX <- *The MAX() function returns the largest value of the selected column.*
 - BINARY_CHECKSUM <- **
 - MIN <- *Returns the minimum value in the expression. May be followed by the OVER clause.*
 - CHECKSUM <- *The CHECKSUM is intended to build a hash index based on an expression or column list. *
 - SUM <- *Returns the sum of all the values, or only the DISTINCT values, in the expression. SUM can be used with numeric columns only. Null values are ignored.*
 - CHECKSUM_AVG  <- *Returns the checksum of the values in a group. Null values are ignored. Can be followed by the OVER clause.*
 - STDEV <- *Returns the statistical standard deviation of all values in the specified expression.*
 - COUNT <- *The COUNT() function returns the number of rows that matches a specified criteria.* 
    __COUNT(distinct)__ <- ```SELECT COUNT(distinct salary) from emp;```
 - STDEVP <- *Returns the statistical standard deviation for the population for all values in the specified expression.*
 - COUNT_BIG <- *Returns the number of items in a group. COUNT_BIG works like the COUNT function. The only difference between the two functions is their return values. COUNT_BIG always returns a bigint data type value. COUNT always returns an int data type value.*
 - VAR <- *Returns the statistical variance of all values in the specified expression. May be followed by the OVER clause.*
 - GROUPING <- *Indicates whether a specified column expression in a GROUP BY list is aggregated or not. GROUPING returns 1 for aggregated or 0 for not aggregated in the result set. GROUPING can be used only in the SELECT <select> list, HAVING, and ORDER BY clauses when GROUP BY is specified.*
 - VARP <- *Returns the statistical variance for the population for all values in the specified expression.*

#### Scalar Functions
 - UPPER <- Returns a character expression with lowercase character data converted to uppercase.
    ```
    SELECT UPPER(RTRIM(LastName)) + ', ' + FirstName AS Name  
    FROM Person.Person  
    ORDER BY LastName;  
    ```
 - LOWER <- Returns a character expression after converting uppercase character data to lowercase.
    ```
    SELECT LOWER(SUBSTRING(Name, 1, 20)) AS Lower,   
    UPPER(SUBSTRING(Name, 1, 20)) AS Upper,   
    LOWER(UPPER(SUBSTRING(Name, 1, 20))) As LowerUpper  
    FROM Production.Product  
    WHERE ListPrice between 11.00 and 20.00;
    ```
 - LEN <- Returns the number of characters of the specified string expression, excluding trailing blanks.
    ```
    SELECT LEN(FirstName) AS Length, FirstName, LastName   
    FROM Sales.vIndividualCustomer  
    WHERE CountryRegionName = 'Australia';
    ```
 - CONCAT <- Returns a string that is the result of concatenating two or more string values.
    ```
   SELECT CONCAT( emp_name, emp_middlename, emp_lastname ) AS Result  FROM #temp;
    ```
 - REPLICATE <- Repeats a string value a specified number of times.
    ```
    SELECT [Name]  
    , REPLICATE('0', 4) + [ProductLine] AS 'Line Code'  
    FROM [Production].[Product]  
    WHERE [ProductLine] = 'T'  
    ORDER BY [Name]; 
    
    RESUL:
    Name                                               Line Code  
    -------------------------------------------------- ---------  
    HL Touring Frame - Blue, 46                        0000T   
    HL Touring Frame - Blue, 50                        0000T   
    HL Touring Frame - Blue, 54                        0000T   
    ...  
    ```
 - REVERSE <- Returns the reverse order of a string value.
    ```
    SELECT FirstName, REVERSE(FirstName) AS Reverse  
    FROM Person.Person  
    WHERE BusinessEntityID < 5  
    ORDER BY FirstName;  
    ```
 - SUBSTRING <- Returns part of a character, binary, text, or image expression in SQL Server.
    ```
    SELECT name, SUBSTRING(name, 1, 1) AS Initial ,
    SUBSTRING(name, 3, 2) AS ThirdAndFourthCharacters
    FROM sys.databases  
    WHERE database_id < 5;
    ```
#### Select only Firt one
```SELECT TOP 1 column_name FROM table_name ORDER BY column_name ASC;```
#### Select only Last one
```SELECT TOP 1 column_name FROM table_name ORDER BY column_name DESC;```

### SQL Server Date Functions
 - DATEADD (datepart, number, date)
 - DATEDIFF (datepart, start, end)
 - DATENAME (datepart, date)
 - DATEPART (datepart, date)
 - DAY (date)
 - GETDATE()
 - GETUTCDATE()
 - MONTH (date)
 - YEAR (date)
### SQL Server Mathematical Functions
 - ABS
 - LOG10
 - ACOS
 - PI
 - ASIN
 - POWER
 - ATAN
 - RADIANS
 - ATN2
 - RAND
 - CEILING
 - ROUND
 - COS
 - SIGN
 - COT
 - SIN
 - DEGREES
 - SQUARE
 - EXP
 - SQRT
 - FLOOR
 - TAN
 - LOG




### DECLARE and SET Varibales
```
DECLARE @Mojo int
SET @Mojo = 1
SELECT @Mojo = Column FROM Table WHERE id=1
IF / ELSE IF / ELSE Statement
IF @Mojo < 1
  BEGIN
	PRINT 'Mojo Is less than 1'
  END
ELSE IF @Mojo = 1
  BEGIN
    PRINT 'Mojo Is 1'
  END
ELSE
  BEGIN
	PRINT 'Mojo greater than 1'
  END
```
### CASE Statement
```
SELECT Day = CASE 
  WHEN (DateAdded IS NULL) THEN 'Unknown'
  WHEN (DateDiff(day, DateAdded, getdate()) = 0) THEN 'Today'
  WHEN (DateDiff(day, DateAdded, getdate()) = 1) THEN 'Yesterday'
  WHEN (DateDiff(day, DateAdded, getdate()) = -1) THEN 'Tomorrow'
  ELSE DATENAME(dw , DateAdded) 
END
FROM Table
```
### Add A Column
```
ALTER TABLE YourTableName ADD [ColumnName] [int] NULL;
```
### Rename a Column
```
EXEC sp_rename 'TableName.OldColName', 'NewColName','COLUMN';
```
### Rename a Table
```
EXEC sp_rename 'OldTableName', 'NewTableName';
```
### Add a Foreign Key
```
ALTER TABLE Products WITH CHECK 
ADD CONSTRAINT [FK_Prod_Man] FOREIGN KEY(ManufacturerID)
REFERENCES Manufacturers (ID);
```
### Add a NULL Constraint
```
ALTER TABLE TableName ALTER COLUMN ColumnName int NOT NULL;
```
### Set Default Value for Column
```
ALTER TABLE TableName ADD CONSTRAINT 
DF_TableName_ColumnName DEFAULT 0 FOR ColumnName;
```
### Create an Index
```
CREATE INDEX IX_Index_Name ON Table(Columns)
```
### Check Constraint
```
ALTER TABLE TableName
ADD CONSTRAINT CK_CheckName CHECK (ColumnValue > 1)
```
### DROP a Column
```
ALTER TABLE TableName DROP COLUMN ColumnName;
```
### Single Line Comments
```
SET @mojo = 1 --THIS IS A COMMENT
```
### Multi-Line Comments
```
/* This is a comment
	that can span
	multiple lines
*/
```
### Try / Catch Statements
```
BEGIN TRY
	-- try / catch requires SQLServer 2005 
	-- run your code here
END TRY
BEGIN CATCH
	PRINT 'Error Number: ' + str(error_number()) 
	PRINT 'Line Number: ' + str(error_line())
	PRINT error_message()
	-- handle error condition
END CATCH
```
### While Loop
```
DECLARE @i int
SET @i = 0
WHILE (@i < 10)
BEGIN
	SET @i = @i + 1
	PRINT @i
	IF (@i >= 10)
		BREAK
	ELSE
		CONTINUE
END
```
### CREATE a Table
```
CREATE TABLE TheNameOfYourTable (
  ID INT NOT NULL IDENTITY(1,1),
  DateAdded DATETIME DEFAULT(getdate()) NOT NULL,
  Description VARCHAR(100) NULL,
  IsGood BIT DEFAULT(0) NOT NULL,
  TotalPrice MONEY NOT NULL,  
  CategoryID int NOT NULL REFERENCES Categories(ID),
  PRIMARY KEY (ID)
);
```
### User Defined Function
```
CREATE FUNCTION dbo.DoStuff(@ID int)
RETURNS int
AS
BEGIN
  DECLARE @result int
  IF @ID = 0
	BEGIN
		RETURN 0
	END
  SELECT @result = COUNT(*) 
  FROM table WHERE ID = @ID
  RETURN @result
END
GO
SELECT dbo.DoStuff(0)
```

### JOINS
 ![joins](http://i.imgur.com/iJeunLI.jpg "joins") 

### Date Selection on WHERE using (AND, OR, BETWEEN condigions)

 - ``` WHERE date = '20091018' AND date = '20101210'```
 - ``` WHERE date = '20091018' OR ( customerID=224423 AND date = '20101210' )```
 - ``` WHERE date BETWEEN '20091018' AND '20101210'```
 - ``` WHERE Name LIKE | NOT LIKE '%ema*'```
 - ``` WHERE SomeID IS NULL | IS NOT NULL```

### GROUP BY / ORDER BY / HAVING
 - ``` SELECT * FROM Employees ORDER BY 2,3```
 - ``` SELECT CustomerId,Invoices FROM Sales GROUP BY CUstomerId```

##### *Group by ofter WHERE clause:*
``` SELECT * FROM Table WHERE someID > 200 GROUP BY zipCode```


##### *Using HAVING clause:*
``` 
SELECT CustomerId,COUNT(*) AS Orders 
FROM Table 
WHERE 
    salary > 3 
GROUP BY CustomerID 
    HAVING COUNT(*) >= 4



SELECT shipdate,salesorderdate,customerid 
CASE 
    WHEN Freight < 25 THEN 'lite'
    WHEN Freight BETWEEN 25 AND 100 THEN 'normal'
    WHEN Freight > 101 THEN 'heavy'
    ELSE 'None'
    END FreightType -- this is a new column
FROM schemanamespace.tablename
WHERE 
    condition
```

### UPDATE command

```
UPDATE table-name set column-name = value where condition;
```

### Delete command
```
DELETE from table-name WHERE condition;
```

### Sub-Queries with ALL,ANY,IN and EXISTS

#### IN conditional
```
SELECT CustomerId,Firstname,lastname FROM CUstomers
WHERE CustomerId IN
    ( SELECT CUstomerId FROM Sales WHERE SupplyId LIKE'C*')
    -- You can chain more In statements from here
    IN(SELECT...)
SELECT CustomerID,Max(DateSold)
```
#### EXISTS conditional
```
SELECT * FROM Customers
WHERE EXISTS
(SELECT * FROM Sales WHERE CustomerID = SalesID)
```
#### ANY (CAN USE ANY COMPARISON OPERATOR <,>,<>,= ANY )
```
SELECT CustomerId,Firstname,lastname FROM CUstomers
WHERE City = ANY(SELECT City FROM Customers WHERE State = 'FL)
```

#### ALL (CAN USE ANY COMPARISON OPERATOR <,>,<>,= ANY )
```
SELECT CustomerId,Firstname,lastname FROM CUstomers
WHERE ZipCore > ALL (SELECT ZipCode FROM Customers Where State = 'CA')
```
### Sub queries
This SQL Server tutorial explains how to use subqueries in SQL Server (Transact-SQL) with syntax and examples.
__What is a subquery in SQL Server?__
In SQL Server, a subquery is a query within a query. You can create subqueries within your SQL statements. These subqueries can reside in the WHERE clause, the FROM clause, or the SELECT clause.
Note
 - In SQL Server (Transact-SQL), a subquery is also called an INNER QUERY or INNER SELECT.
 - In SQL Server (Transact-SQL), the main query that contains the subquery is also called the OUTER QUERY or OUTER SELECT.
##### WHERE clause
Most often, the subquery will be found in the WHERE clause. These subqueries are also called nested subqueries.
For example:
```
SELECT p.product_id, p.product_name
FROM products p
WHERE p.product_id IN
   (SELECT inv.product_id
    FROM inventory inv
    WHERE inv.quantity > 10);
```
The subquery portion of the SELECT statement above is:
```
(SELECT inv.product_id
 FROM inventory inv
 WHERE inv.quantity > 10);
 ```
This subquery allows you to find all product_id values from the inventory table that have a quantity greater than 10. The subquery is then used to filter the results from the main query using the IN condition.

This subquery could have alternatively been written as an INNER join as follows:
```
SELECT p.product_id, p.product_name
FROM products p
INNER JOIN inventory inv
ON p.product_id = inv.product_id
WHERE inv.quantity > 10;
```
This INNER JOIN would run more efficiently than the original subquery. It is important to note, though, that not all subqueries can be rewritten using joins.

#### FROM clause

A subquery can also be found in the FROM clause. These are called inline views.

For example:
```
SELECT suppliers.supplier_name, subquery1.total_amt
FROM suppliers,
 (SELECT supplier_id, SUM(orders.amount) AS total_amt
  FROM orders
  GROUP BY supplier_id) subquery1
WHERE subquery1.supplier_id = suppliers.supplier_id;
```
In this example, we've created a subquery in the FROM clause as follows:
```
(SELECT supplier_id, SUM(orders.amount) AS total_amt
 FROM orders
 GROUP BY supplier_id) subquery1
```
This subquery has been aliased with the name subquery1. This will be the name used to reference this subquery or any of its fields.

#### SELECT clause

A subquery can also be found in the SELECT clause. These are generally used when you wish to retrieve a calculation using an aggregate function such as the SUM, COUNT, MIN, or MAX function, but you do not want the aggregate function to apply to the main query.

For example:
```
SELECT e1.last_name, e1.first_name,
  (SELECT MAX(salary)
   FROM employees e2
   WHERE e1.employee_id = e2.employee_id) subquery2
FROM employees e1;
```
In this example, we've created a subquery in the SELECT clause as follows:
```
(SELECT MAX(salary)
 FROM employees e2
 WHERE e1.employee_id = e2.employee_id) subquery2
 ```
The subquery has been aliased with the name subquery2. This will be the name used to reference this subquery or any of its fields.

The trick to placing a subquery in the select clause is that the subquery must return a single value. This is why an aggregate function such as the SUM, COUNT, MIN, or MAX function is commonly used in the subquery.

### Constraints
In SQL, we have the following constraints:

NOT NULL - Indicates that a column cannot store NULL value
UNIQUE - Ensures that each row for a column must have a unique value
PRIMARY KEY - A combination of a NOT NULL and UNIQUE. Ensures that a column (or combination of two or more columns) have a unique identity which helps to find a particular record in a table more easily and quickly
FOREIGN KEY - Ensure the referential integrity of the data in one table to match values in another table
CHECK - Ensures that the value in a column meets a specific condition
DEFAULT - Specifies a default value for a column

```
CREATE TABLE PersonsNotNull(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
...
)
```
```
CREATE TABLE Persons(
P_Id int NOT NULL UNIQUE,
LastName varchar(255) NOT NULL,
...
)
```
```
CREATE TABLE Persons(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
CONSTRAINT pk_PersonID PRIMARY KEY (P_Id,LastName)
)
```
```
CREATE TABLE Orders(
O_Id int NOT NULL PRIMARY KEY,
OrderNo int NOT NULL,
P_Id int FOREIGN KEY REFERENCES Persons(P_Id)
)
```
```
CREATE TABLE Persons(
P_Id int NOT NULL CHECK (P_Id>0),
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
)
```
```
CREATE TABLE Persons(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
CONSTRAINT chk_Person CHECK (P_Id>0 AND City='Sandnes')
)
```

```
CREATE TABLE Persons(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255) DEFAULT 'Sandnes'
)
```

```
IF object_id('BossExists') IS NOT NULL DROP FUNCTION BossExists;
GO

create function dbo.BossExists(@id int)
returns int
AS 
BEGIN
	DECLARE @temp int;
	if @id is null return 0;
	SET @temp = (select distinct mgr from emp where mgr=@id);
	if @temp is null return 0;
	return 1;
END;
GO

select dbo.BossExists(50);
select dbo.BossExists(7698);

IF object_id('NotBusyBoss') IS NOT NULL DROP FUNCTION NotBusyBoss;
GO

create function dbo.NotBusyBoss(@id int)
returns int
AS
BEGIN
	DECLARE @temp int;
	if @id is null return 0;
	SET @temp = (select count(1) from emp where mgr=@id);
	if (@temp is null OR @temp>1) return 0;
	return 1;
END;
GO

select dbo.NotBusyBoss(7698);
select dbo.NotBusyBoss(7788);

IF object_id('JobExists') IS NOT NULL DROP FUNCTION JobExists;
GO

create function dbo.JobExists(@job varchar(50))
returns int
AS
BEGIN	
	DECLARE @temp int;
	SET @temp = (select count(1) from emp where job=@job);
	if (@job is null OR @temp is null OR @temp=0) return 0;
	return 1;
END;
GO

select dbo.JobExists('BAKER');
select dbo.JobExists('CLERK');

IF object_id('GoodJobSalary') IS NOT NULL DROP FUNCTION GoodJobSalary;
GO

create function dbo.GoodJobSalary(@job varchar(50), @salary int)
returns int
AS
BEGIN	
	DECLARE @temp int;
	SET @temp = (select avg(sal) from emp where job=@job);
	if (@job is null OR @salary is null OR @temp is null OR @temp>@salary) return 0;
	return 1;
END;
GO

select dbo.GoodJobSalary('CLERK', (select avg(sal)-1 from emp where job='CLERK') );
select dbo.GoodJobSalary('CLERK', (select avg(sal) from emp where job='CLERK') );

IF object_id('empX') IS NOT NULL DROP TABLE empX;
select * into dbo.empX from emp;

IF object_id('trgChecks') IS NOT NULL DROP TRIGGER trgChecks;
GO

CREATE TRIGGER dbo.trgChecks
ON empX
INSTEAD OF INSERT
AS 
BEGIN
	DECLARE db_cursor CURSOR FOR SELECT empno, mgr, job, sal FROM inserted;
	DECLARE @empno int, @mgr int, @job varchar(100), @sal int;

	OPEN db_cursor  
	BEGIN TRAN
	BEGIN TRY
			FETCH NEXT FROM db_cursor INTO @empno, @mgr, @job, @sal
			WHILE @@FETCH_STATUS = 0  
			BEGIN  
				PRINT concat('CHECKING EMPNO ', @empno, '; MGR ', @mgr, '; JOB ', @job, '; SAL ', @sal, '...');
				-- RAISERROR('Boss does not exist!', 17);
				if (dbo.BossExists(@mgr)<>1) THROW 50000, 'Boss does not exist!', 1
				if (dbo.NotBusyBoss(@mgr)<>1) THROW 50001, 'Busy Boss!', 2
				if (dbo.JobExists(@job)<>1) THROW 50001, 'Job does not exist!', 3
				if (dbo.GoodJobSalary(@job, @sal)<>1) THROW 50001, 'Bad job salary!', 4
				INSERT INTO empX SELECT * FROM inserted WHERE EMPNO=@empno;
				FETCH NEXT FROM db_cursor INTO @empno, @mgr, @job, @sal
				PRINT concat('INSERTED EMPNO ', @empno, '; MGR ', @mgr, '; JOB ', @job, '; SAL ', @sal, '...');
			END  
			COMMIT TRAN;
	END TRY
	BEGIN CATCH
		PRINT 'ERRORS CAUGHT...';
		ROLLBACK TRAN;
		THROW;
	END CATCH
	CLOSE db_cursor;
	DEALLOCATE db_cursor;
END;
GO

SELECT COUNT(1) from empX;
-- bad mgr
INSERT INTO empX (empno, mgr, job, sal, deptno) values (10, 6666, 'BAKER', 10, 50); 
GO
-- busy mgr
INSERT INTO empX (empno, mgr, job, sal, deptno) values (10, 7698, 'BAKER', 10, 50); 
GO
-- bad job
INSERT INTO empX (empno, mgr, job, sal, deptno) values (10, 7788, 'BAKER', 10, 50); 
GO
-- bad jobsal
INSERT INTO empX (empno, mgr, job, sal, deptno) values (10, 7788, 'CLERK', 10, 50); 
GO
-- all OK
INSERT INTO empX (empno, mgr, job, sal, deptno) values (10, 7788, 'CLERK', 2000, 50); 
GO
SELECT COUNT(1) from empX;
```








```
BEGIN TRANSACTION [Tran1]

BEGIN TRY

INSERT INTO [Test].[dbo].[T1]
  ([Title], [AVG])
VALUES ('Tidd130', 130), ('Tidd230', 230)

UPDATE [Test].[dbo].[T1]
  SET [Title] = N'az2' ,[AVG] = 1
WHERE [dbo].[T1].[Title] = N'az'


COMMIT TRANSACTION [Tran1]

END TRY
BEGIN CATCH
  ROLLBACK TRANSACTION [Tran1]
END CATCH  

GO
```









