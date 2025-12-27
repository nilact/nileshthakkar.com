---
title: 'Web API Response Container'
date: 2016-06-17T18:12:00.000+05:30
draft: true
url: 
---

using System;  
using System.Collections.Generic;  
using System.Linq;  
using System.Net;  
using System.Net.Http;  
using System.Runtime.Serialization;  
using System.Web.Http;  
  
namespace WebApiSer.Controllers  
{  
public class ValuesController : ApiController  
{  
// GET api/values  
public HttpResponseMessage Get()  
{  
IEnumerable res = new Person\[\] { new Person() { Name = "value1" }, new Person() { Name = "value2" }, new Person() { Name = "value3" } };  
  
//var response = new Response, Person>(res);  
//return Request.CreateResponse(HttpStatusCode.OK, response, Request.Content.Headers.ContentType.MediaType);  
  
return Request.CreateResponse(HttpStatusCode.OK, res);  
}  
  
// GET api/values/5  
public string Get(int id)  
{  
return "value";  
}  
}  
  
\[DataContract(Namespace = "", Name="Response")\]  
public class Response  
where C : IEnumerable  
where T : class  
{  
public Response(C data)  
{  
Result = data;  
Count = data.Count();  
}  
  
\[DataMember\]  
public int Count { get; set; }  
  
\[DataMember\]  
public C Result { get; set; }  
}  
  
\[DataContract(Namespace="")\]  
public class Person  
{  
\[DataMember\]  
public string Name { get; set; }  
}  
}