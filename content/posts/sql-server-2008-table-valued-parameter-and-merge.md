---
title: 'SQL Server 2008 : Table Valued Parameter and Merge'
date: 2009-11-19T22:57:00.006+05:30
draft: false
url: /2009/11/sql-server-2008-table-valued-parameter.html
tags: 
- tsql
- SQL Server 2008
- SQL Server
---

Recently I was working on one project and I was supposed to Insert/Update bunch of records in database. I wanted to do it in a single round trip. I Google about it and found out two excellent features of SQL Server 2008: Table Valued Parameter and Merge Statement.  

Table Valued Parameter allows you to send bunch of records to database in single round trip. To know how it can be done, please refer this [link](http://blogs.microsoft.co.il/blogs/bursteg/archive/2007/12/02/sql-server-2008-table-valued-parameters.aspx)  

Merge Statement is another good thing that you’ll fall in love with. It provides an efficient way to perform multiple DML operation. To know how it can be useful, please check out this [link](http://technet.microsoft.com/en-us/library/bb510625.aspx)  

Also check out one example of Merge [here](http://blog.sqlauthority.com/2008/08/28/sql-server-2008-introduction-to-merge-statement-one-statement-for-insert-update-delete/)  

I wanted to use Table Valued Parameters with Merge Command and I did it this way:  

```sql
Create Procedure vi_GroupRightsAccess_AddUpdate
--below is the Table valued Parameter of type tv_GroupRight
--user defined table valued type created in db, and it should be read only
@tvpGrpRightAccess tv_GroupRight Readonly
As
Begin
--vi_GroupRightAccess is the table in my database
MERGE vi_GroupRightsAccess_Detail AS TargetTable
--Whatever columns you define in Using clause will be available in join
USING (SELECT GroupID,ModuleID,AccessID FROM @tvpGrpRightAccess) AS TvpSource
ON TargetTable.dtgroGroupId= TvpSource.GroupId and TargetTable.dtgroModuleId=TvpSource.ModuleId
--Perform intended operation if record matches
WHEN MATCHED THEN UPDATE SET TargetTable.dtgroAccessId = TvpSource.AccessID
--do what you want if it doesn’t match
WHEN NOT MATCHED THEN
INSERT(dtgroGroupId,dtgroModuleID,dtgroAccessID)
VALUES(TvpSource.Groupid,TvpSource.ModuleId,TvpSource.AccessID);
End
```

I hope this will be helpful.  

Enjoy.. ;)