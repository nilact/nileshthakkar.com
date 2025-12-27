---
title: 'How to pass multiple records to stored procedure in Asp.net?'
date: 2012-05-08T01:14:00.000+05:30
draft: false
url: /2012/05/how-to-pass-multiple-records-to-stored.html
tags: 
- tsql
- XML
- SQL Server
- Asp.net
---

There are mainly three ways you can pass multiple records to stored procedure.  

*   Comma delimited string:  It is suitable for the scenario where you need to pass single column value
*   XML document : You need to support SQL Server 2005 and you’ve multiple column values.
*   Table valued parameter : You’ve SQL Server 2008 and you don’t want to get into parsing stuff at the backend and want the best performance.

To know what is table valued parameter and how to pass datatable to stored procedure and implement upsert (update + insert) logic using Merge Statement, check out an old post [here](http://nilthakkar.blogspot.in/2009/11/sql-server-2008-table-valued-parameter.html).  

We’ll see in this post how you can pass XML document to stored procedure and parse it in stored procedure and insert into table.  

First of all let’s check out how you can generate XML Document in Asp.net using [XmlSerializer](http://msdn.microsoft.com/en-us/library/system.xml.serialization.xmlserializer%28v=vs.90%29.aspx).  

Let’s say we’ve list of Person and we want to serealize it to xml then do as below:  

```csharp
List<Person> people = new List<Person> {
     new Person { FirstName = "Scott", LastName = "Guthrie", Age = 32 },
     new Person { FirstName = "Bill", LastName = "Gates", Age = 50 },
     new Person { FirstName = "Susanne", LastName = "Guthrie", Age = 32 }
};

string strXML = SerializeObject<List<Person>>(people);
```

And the function that serialize the object to XML is as below :  

```csharp
public string SerializeObject<T>(T Obj)
{
    string strxml = string.Empty;
    using (StringWriter sw = new StringWriter())
    {
        XmlSerializer xs = new XmlSerializer(typeof(T));
        xs.Serialize(sw, Obj);
        strxml = sw.ToString();
    }
    return strxml;
}
```

And the resultant XML would be as below :  

```xml
<?xml version="1.0" encoding="utf-16"?>
<ArrayOfPerson xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Person>
<FirstName>Scott</FirstName>
<LastName>Guthrie</LastName>
<Age>32</Age>
</Person>
<Person>
<FirstName>Bill</FirstName>
<LastName>Gates</LastName>
<Age>50</Age>
</Person>
<Person>
<FirstName>Susanne</FirstName>
<LastName>Guthrie</LastName>
<Age>32</Age>
</Person>
</ArrayOfPerson>
```

Generated XML is having encoding UTF-16. Now pass above xml to your stored procedure as shown below:  

```csharp
string connectionString = Convert.ToString(ConfigurationManager.ConnectionStrings["XMLDBConnString"]);          

using (SqlConnection cn = new SqlConnection(connectionString))
{
    using (SqlCommand cm = new SqlCommand("SaveUser", cn))
    {
        cm.CommandType = CommandType.StoredProcedure;

        cm.Parameters.Add("@UsersXml", SqlDbType.NVarChar, -1).Value = strXML;
        
        cn.Open();
        
        cm.ExecuteNonQuery();
    }
}
```

If you see the parameter datatype, it is Nvarchar as generated XML is having encoding UTF-16. If you need to support Unicode characters then you don’t need to change anything or you need to encode to UTF-8, then you can use UTF8StringWriter class  shown below:  

```csharp
public class Utf8StringWriter : StringWriter
{
    public override Encoding Encoding
    {
        get { return Encoding.UTF8; }
    }
}
```

Note : If you pass XML having enconding UTF-16 with varchar datatype to  stored procedure, it will throw error : **unable to switch the encoding**  

To avoid error either use Utf8StringWriter class instead of StringWriter and use varchar datatype or use nvarchar datatype and pass xml with encoding UTF-16.  

Now to parse generated element centric xml we need to use Xquery. If you’re new to Xquery, I would suggest you go through below links or you can skip them if you wish.  

[Xpath Syntax](http://www.w3schools.com/xpath/xpath_syntax.asp)  
[Get Table From XML](http://www.playwithsql.com/2011/12/get-table-from-xml.html)  

Check out sample stored procedure :  

```sql
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
ALTER PROCEDURE [dbo].[SaveUser]
    @UsersXml AS NVARCHAR(MAX)
AS
BEGIN
    DECLARE @XML AS XML
       
    SELECT @XML = @UsersXml
    
    INSERT INTO Person (FirstName, LastName, Age)      
    select M.Item.value('FirstName[1]','VARCHAR(50)'),
    M.Item.value('LastName[1]','Varchar(50)'),
    M.Item.value('Age[1]','INT')
    FROM @xml.nodes('/ArrayOfPerson/Person') AS M(Item)
END
```

Note : XQuery is case-sensitive, hence check your tsql statements for typo if you don’t get expected output.  

That’s all you need, I hope it helps! Your comments are most welcome. :)