title: Some guidelines on writing a better SQL query
date: 2015-07-07 00:37:17
author: Toh Leng Wee
tags: [sql, query]
---

## About
There are many ways on writing a SQL query. However, there are some guidelines that could help us to improve our query performance. Efficient query is important especially when we use store procudure in our system. In this post, we will show you some of the guidelines.

## Limit selected list
We shall always specify the columns we need in **SELECT**. Avoid to use ** * ** in our select list to return some unused columns in our system.

```sql
--Bad example
SELECT *
FROM [Customer] 
WHERE [CustomerId] = @CustomerId 

--Better example
SELECT [FirstName], [LastName], [DateOfBirth]
FROM [dbo].[Customer]
WHERE [CustomerId] = @CustomerId
```

## Use Search Argument (SARG)
Always try to change Non-Search Argument to [Search Argument](http://www.sqlconsulting.com/news1109.htm) for Index Column in **WHERE** clause.

**Search Arguement**: 
-operators include **=**, **>**, **<**, **>=**, **<=**, **IN**, **BETWEEN**
-Like 'literal%''

**Non-Search Argument**:
-Exclusion conditions E.g  **<>**, **!=**, **!>**, **NOT EXISTS**
-Like '%literal'
-Arithmetic operations, E.g  orderid*2 = 3400
-Function, E.g subsring(username,1,1) = 'x'

```sql
--Bad example
SELECT [FirstName], [LastName], [DateOfBirth]
FROM [dbo].[Customer]
WHERE CASE @DateFilter
      WHEN 'Year' THEN [Year]
      WHEN 'Month' THEN [Month]
      ELSE [Day] END = @Date

--Better example
IF @DateFilter = 'Year'
    SELECT [FirstName], [LastName], [DateOfBirth]
    FROM [dbo].[Customer]
    WHERE [Year] = @Date
ELSE IF @DateFilter = 'Month'
    SELECT [FirstName], [LastName], [DateOfBirth]
    FROM [dbo].[Customer]
    WHERE [Month] = @Date
ELSE
    SELECT [FirstName], [LastName], [DateOfBirth]
    FROM [dbo].[Customer]
    WHERE [Day] = @Date
```
## Avoid unnecessary data conversion
-Define store procedure parameters data type to be the same as table schema data type.
-Don't join tables with different data type

```sql
--Bad example
CREATE PROCEDURE [dbo].[GetLoginName]
    @CustomerId varchar(10)
AS
    SELECT [LoginName]
    FROM [Customer]
    WHERE [CustomerId] = @CustomerId --[CustomerId] is int in table schema

--Better example
CREATE PROCEDURE [dbo].[GetLoginName]
    @CustomerId int
AS
    SELECT [LoginName]
    FROM [Customer]
    WHERE [CustomerId] = @CustomerId --[CustomerId] is int in table schema
```

## Use EXISTS over COUNT(1) if you want to verify data existence
Sometimes when we want to verify data existence, we will use COUNT(1) to achieve it. But COUNT(1) will actuall scan the who table even the record was found. So EXISTS would be a better way in this case.

```sql
--Bad example
DECLARE @isExists as int
SELECT @isExists = COUNT(1)
FROM [dbo].[Customer]
WHERE CustomerId = 123

--Better example
IF EXISTS(SELECT TOP 1 1 FROM [dbo].[Customer] WHERE CustomerId = 123)
```

## Use UNION ALL over UNION
UNION removes duplicate records (where all columns in the results are the same)and UNION ALL does not.
There is a performance hit when using UNION vs UNION ALL, since the database server need to do additional work to remove the duplicate rows, but usually you do not want the duplicates (especially when developing reports). Therefore, you have to deal with the duplicated data in your application.

```sql
--Bad example
SELECT [CustomerId] FROM [Customer]
UNION
SELECT [CustomerId] FROM [DeleteCustomer]

--Better example
SELECT [CustomerId] FROM [Customer]
UNION ALL
SELECT [CustomerId] FROM [DeleteCustomer]
```

## Summary
We could always try to refine our query to do less work but still achieve the same outcome.
Always try to use index in your WHERE clause, you could use **sp_helpindex** to know the table indexes.
```sql
sp_helpindex '[TableName]'
```
Keep data table size as small as possible, purge history records or unused records.