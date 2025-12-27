---
title: 'Update with join and unexpected output'
date: 2010-03-30T23:17:00.001+05:30
draft: false
url: /2010/03/update-with-join-and-unexpected-output.html
tags: 
- Join
- tsql
- SQL Server
---

Recently my one of the colleague was writing trigger and in that he’s having Update Query with join, syntax and everything was right but he’s not getting the expected output.  To understand the situation let’s take an example.

Create two temporary tables 

```sql
CREATE TABLE #tmpTable1
( gid smallint,
  gBal smallint
)
CREATE TABLE #tmpTable2
( tid smallint,
  tBal smallint
)
```

Inset following values in both the tables.  

```sql
insert into #tmpTable1(gid,gBal) values(1, 200), (2,300)
insert into #tmpTable2(tid,tBal) values(1, 300), (2,400),(1, 600), (2,700)
```

Now both the table contain inserted values:  

![SQL Insert Query Output](/images/bb3ef-untitled_thumb5b25d.jpg)  
The update query with join that was not giving expected output :  

```sql
update #tmpTable1 set gBal = gBal +  tbal
from #tmpTable1 inner join #tmpTable2 
on gid=tid 
```

As you can see nothing is wrong with the query, but it is updating it with the first two values of #tmpTable2 and hence the output will be as shown below but output should be 1100 for record no 1 and 1400 for record no 2.  

![Output](/images/db044-output_thumb.jpg)  

As per the MSDN Documentation: **single UPDATE statement never updates the same row twice.**  

Let’s say that you have more than one sale  for a given item and you update as shown above, It does not work correctly. It executes without error, but each item is updated with only one sale, regardless of how many sales actually occurred on that day. In the situation in which more than one sale for a given title can occur on the same day, all the sales for each title must be aggregated together within the UPDATE statement, as shown in following query:

```sql
Update #tmpTable1
SET gBal = gbal +
(SELECT SUM(tbal)
FROM #tmpTable2 AS tmp
WHERE tmp.tid = gid)
```

This will give us the expected output that is   

![Output With Update Query and Join](/images/2b7cd-output20with20update20query20and20join_thumb.jpg) That’s it!!  Enjoy ;)