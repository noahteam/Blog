title: Introduction to Table-Valued Parameter
date: 2015-11-16 01:25:19
tags:
---
##About
Table valued parameters is a way to pass Datatable as a parameter to a stored procedure.

## Dapper way 
The object has to be converted into Datatable in order to be passed as table valued parameter to SQL server.


## SqlCommand way
Set *SqlParameter.SqlDbType = SqlDbType.Structured;* and *SqlParameter.TypeName = {Table-Valued Parameter name}*

## Pros
1. Connect to DB one time, so that connection overhead can be reduced.
2. Easy to handle multiple updates in stored procedure.
3. Cached upon frequent use
4. More intuitive as application passing a list of class object to SQL server.

## Cons
1. Table-valued parameters can only be indexed to support UNIQUE or PRIMARY KEY constraints.
2. SQL server does not maintain statistics on the TVP Columns.
3. The input is ReadOnly parameter, cannot perform DML operation (UPDATE, DELETE, or INSERT) on it.
4. Cannot pass table valued parameter to CLR user-defined function

## Things to take note
If needs to alter and release a existing table valued parameter, it requires drop all dependencies. For example, recreate those stored procedure to use a new table valued parameter.

##Reference
[https://msdn.microsoft.com/en-us/library/bb675163.aspx](https://msdn.microsoft.com/en-us/library/bb675163.aspx)
[https://msdn.microsoft.com/en-us/library/bb510489.aspx](https://msdn.microsoft.com/en-us/library/bb510489.aspx)
[http://www.dbdelta.com/maximizing-performance-with-table-valued-parameters/](http://www.dbdelta.com/maximizing-performance-with-table-valued-parameters/)
[http://www.codeproject.com/Articles/835519/Passing-Table-Valued-Parameters-with-Dapper](http://www.codeproject.com/Articles/835519/Passing-Table-Valued-Parameters-with-Dapper)