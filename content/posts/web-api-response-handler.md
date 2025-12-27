---
title: 'Web API Response Handler'
date: 2016-06-17T18:10:00.001+05:30
draft: true
url: 
---

using System;  
using System.Collections.Generic;  
using System.Linq;  
using System.Net;  
using System.Net.Http;  
using System.Net.Http.Formatting;  
using System.Net.Http.Headers;  
using System.Reflection;  
using System.Runtime.Serialization;  
using System.Threading;  
using System.Threading.Tasks;  
using System.Web;  
using System.Web.Http;  
using WebApiSer.Controllers;  
  
namespace WebApiSer.App\_Start  
{  
\[KnownType(typeof(Person))\]  
public class CustomResponseHandler : DelegatingHandler  
{  
protected override async Task SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)  
{  
var response = await base.SendAsync(request, cancellationToken);  
  
return BuildApiResponse(request, response);  
}  
  
private HttpResponseMessage BuildApiResponse(HttpRequestMessage request, HttpResponseMessage response)  
{  
IEnumerable content;  
  
response.TryGetContentValue(out content);  
  
//Content negotiation - to return response in the requested format  
var formatter = GlobalConfiguration.Configuration.Formatters.First(  
  t => t.SupportedMediaTypes.Contains(new MediaTypeHeaderValue(response.Content.Headers.ContentType.MediaType)));  
  
Type elementType = content.GetType().GetElementType();  
Type constructedType = typeof(ResponsePackage<>).MakeGenericType(elementType);  
var resPackageObj = Activator.CreateInstance(constructedType, content, (content as IEnumerable).Count());  
  
MethodInfo genericMethod = this.GetType()  
.GetMethod("GetResponse", BindingFlags.NonPublic | BindingFlags.Instance)  
.MakeGenericMethod(constructedType);  
var newResponse = (HttpResponseMessage)genericMethod.Invoke(this, new\[\] { request, response.StatusCode, resPackageObj, formatter });  
  
//var responsePackage = new ResponsePackage(content, (content as IEnumerable).Count());  
//var newResponse = request.CreateResponse(response.StatusCode, res, formatter);  
  
foreach (var header in response.Headers) //Add back the response headers  
{  
newResponse.Headers.Add(header.Key, header.Value);  
}  
  
return newResponse;  
}  
  
private HttpResponseMessage GetResponse(HttpRequestMessage request, HttpStatusCode statusCode, T value, MediaTypeFormatter formatter)  
{  
return request.CreateResponse(statusCode, value, formatter);  
}  
}  
  
\[DataContract(Name="Response", IsReference=true, Namespace="http://cybage.com/sampleschema")\]  
public class ResponsePackage where T : class  
{  
\[DataMember\]  
public IEnumerable Result { get; set; }  
  
\[DataMember\]  
public int Count { get; set; }  
  
public ResponsePackage() { }  
  
public ResponsePackage(IEnumerable result, int count)  
{  
Result = result;  
Count = count;  
}  
}  
  
}