---
title: 'Outer joins and search predicates – II'
date: 2011-07-31T15:51:00.002+05:30
draft: false
url: /2011/07/outer-joins-and-search-predicates-ii.html
tags: 
- tsql
- SQL Server 2008
- SQL Server
---

  
In my [last post](http://nilthakkar.blogspot.com/2011/07/outer-joins-and-search-predicates.html) we saw that if we place search predicate in WHERE clause instead of placing it along with ON clause, we may get expected output. Now let's understand what happens behind the scene.

```
Note : we'll use pubs database for the example. 
```  
Suppose we want to list down all the book titles, then we might write following query.  
```
SELECT T.title\_id as ID, T.Title 
FROM titles T 
ORDER BY T.Title 
```  

Now we are asked to list down sales quantity for any given store for all the titles. If the given title is not sold, the quantity should be zero. For that we might write following query.

  
```
SELECT T.title\_id, T.title, IsNull(S.qty,0) Qty 
FROM titles T 
LEFT JOIN sales S on T.title\_id = S.title\_id 
WHERE S.stor\_id = '6380' 
ORDER BY T.title 
```  

If you run above query, you'll get only two rows which were sold instead of remaining titles with zero quantity. Now if you see the execution plan, you could see that LEFT JOIN is converted to INNER JOIN by the execution engine. Check out below snap.

  
![LEFT JOIN AND PREDICATE IN WHERE CLAUSE](/images/43d8f-left252520join252520and252520predicate252520in252520where252520clause_thumb25255b1225255d.jpg)  

Now if you need to get all the rows with quantity zero if title is not sold, just place your search predicate along with ON clause in LEFT JOIN and you'll get the expected output. So our expected query should be like below :

  
```
SELECT T.title\_id, T.title, IsNull(S.qty,0) Qty 
FROM titles T 
LEFT JOIN sales S on T.title\_id = S.title\_id 
and S.stor\_id = '6380' 
ORDER BY T.title 
```  
Always be careful when using search conditions with OUTER JOIN and construct your queries  
  
as per the output expected. Enjoy!! :)