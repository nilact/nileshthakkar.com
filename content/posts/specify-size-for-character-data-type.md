---
title: 'Specify size for character data type'
date: 2012-01-16T22:39:00.001+05:30
draft: false
url: /2012/01/specify-size-for-character-data-type.html
tags: 
- tsql
- SQL Server
---

Recently I was referring books online to confirm one strange behavior in SQL Server. It was like in one of the legacy application, string was passed to stored procedure as parameter and it was storing only 30 characters. To my surprise when I saw the code, everything seem to be ok. Later on found out the problem and fault was while converting parameter to varchar there was no size defined.  

As per books online,

> When n is not specified in a data definition or variable declaration statement, the default length is 1. When n is not specified when using the CAST and CONVERT functions, the default length is 30.

Check out the below script which will return only first character of parameter being passed instead of parameter value :

```sql
CREATE PROCEDURE GetEmployeeName
    @Name varchar
AS
BEGIN
    SET NOCOUNT ON;
    SELECT @Name
END
GO
```

Now if you define the parameter length, but using cast or convert function, it will return first 30 characters.

```sql
ALTER PROCEDURE GetEmployeeName
    @Name varchar(40)
AS
BEGIN
    SET NOCOUNT ON;
    SELECT Convert(varchar,@Name) as Name
END
GO
--The longest one-word name of Hawaiian boy
exec GetEmployeeName Kananinoheaokuuhomeopuukaimanaalohilo
```

Make sure length is defined for all stored procedure parameters and variables to avoid unexpected behavior.  
Source : [Books Online](http://msdn.microsoft.com/en-us/library/ms176089%28v=SQL.90%29.aspx)