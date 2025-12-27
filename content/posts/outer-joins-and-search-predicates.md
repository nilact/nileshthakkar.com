---
title: 'Outer joins and search predicates'
date: 2011-07-10T17:09:00.000+05:30
draft: false
url: /2011/07/outer-joins-and-search-predicates.html
tags: 
- Join
- tsql
- SQL Server
---

We often use outer joins to construct queries and get expected output, but what if you get unexpected out and the query seem ok. Few days back I experienced the same thing and found the problem with the placement of predicate (search condition).

Let’s take an example :

We’ll use Orders and Customers table from Northwind database.

Let’s check out INNER JOIN and LEFT JOIN result without any condition first.

![Withoutcond](/images/54d04-withoutcond_thumb25255b525255d.jpg) 

As you can see in the snap above both are returning same no of rows ( I’ve taken top(5) for the snap, as both the queries have more than 100 rows)

Now let’s check out INNER JOIN with predicate (search condition) in WHERE and ON clause.

![Innercondition](/images/8033f-innercondition_thumb25255b325255d.jpg)

Both the query returned the same result.

Now let’s check out LEFT JOIN with WHERE clause.

![Lefwhere](/images/c898b-lefwhere_thumb25255b125255d.jpg)

It has also returned the same no of rows, but if we use predicate (search condition) along with ON clause rather than WHERE clause, it will return more no of rows (unexpected output).

![Lefton](/images/3ceb3-lefton_thumb25255b125255d.jpg)

Explanation could be found out on SQL Server Books Online which says,

> There can be predicates that involve only one of the joined tables in the ON clause. Such predicates also can be in the WHERE clause in the query. Although the placement of such predicates does not make a difference for INNER joins, they might cause a different result when OUTER joins are involved. This is because the predicates in the ON clause are applied to the table before the join, whereas the WHERE clause is semantically applied to the result of the join.

An outer join query can produce completely different results depending on how you write it, and where predicates are placed in that query. Take Care!