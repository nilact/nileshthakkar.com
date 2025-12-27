---
title: 'Tip: Schema qualify database objects'
date: 2012-02-13T19:45:00.000+05:30
draft: false
url: /2012/02/tip-schema-qualify-database-objects.html
tags: 
- tsql
- Best Practice
- SQL Server
---

When working in a team, it may happen that few of the team mates follow best practices and others don’t give damn about it. They just completes the tasks on the name of ‘getting things done’. It may create nightmares if all the team members don’t follow the best practices. Either do it or don’t.  
  
Let’s see what can happen with below example.  
  
In one of the project, an SP was created with prefix .dbo and it was working fine until changes came. Now one of the team member made changes and whole team had the feeling “all is well” until that SP was not returning the expected output. So you start debugging your app and consider the following could be the issue.  

*   Being a big Ajax app, you’re getting some Ajax error
*   You’re not passing appropriate parameter and you check it through your code and SQL profiler whether parameters are passed in appropriate manner and with correct values.
*   Check your respective tables for the values.
*   You’re executing SP from SSMS it self to see whether it is working by passing the required parameter values.

You delete the SP and see whether it is returning any value and yes, it is. Later on you found out that SP has **no** prefix and it is executed to default schema name other than .dbo. So there’re two SPs with different schema name. Great!  
  
Let’s say you’re qualifying schema name for your SP but nothing for query inside that SP, it will default to the schema name of the stored procedure. What books online says about it :  

> Inside a stored procedure, object names used with statements (for example, SELECT or INSERT) that are not schema-qualified default to the schema of the stored procedure. If a user who creates a stored procedure does not qualify the name of the tables or views referenced in SELECT, INSERT, UPDATE, or DELETE statements within the stored procedure, access to those tables through the stored procedure is restricted by default to the creator of the procedure.

Make sure you always qualify your database objects with specific schema name or you’re life is doomed.