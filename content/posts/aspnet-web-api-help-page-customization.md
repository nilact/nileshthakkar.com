---
title: "Asp.net Web API Help Page Customization"
description: "Customize ASP.NET Web API Help Page with sorting, multi-project support, and more."
summary: "Customize ASP.NET Web API Help Page with sorting, multi-project support, and more."
date: 2016-09-10T22:44:00.000+05:30
draft: false
url: /2016/09/aspnet-web-api-help-page-customization.html
tags: 
- Customization
- Web API Help
- Web API
- Asp.net
---

After you have setup Web API help page as shown [here](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/creating-api-help-pages), you could do following customizations:

*   List APIs by controller name
*   List Model properties by name
*   Multi-project support (Model classes in separate project)

**List APIs by controller name**

Edit `Areas\HelpPage\Views\Index.cshtml` and replace below line:

```csharp
ILookup<HttpControllerDescriptor, ApiDescription> apiGroups = Model.ToLookup(api => api.ActionDescriptor.ControllerDescriptor);
```

with:

```csharp
ILookup<HttpControllerDescriptor, ApiDescription> apiGroups = Model.OrderBy(d => d.ActionDescriptor.ControllerDescriptor.ControllerName).ToLookup(api => api.ActionDescriptor.ControllerDescriptor);
```

That way it would be easy for anyone to refer to help page documentation and a person can be sure that all the APIs will be in ascending order.

**List Model properties by name**

Default order of the property is the order you added properties to Model class. To list down properties in ascending order, edit `Areas\HelpPage\Views\DisplayTemplates\parameters.cshtml` and replace below line:

```csharp
@foreach (ParameterDescription parameter in Model)
```

with:

```csharp
@foreach (ParameterDescription parameter in Model.OrderBy(c=>c.Name))
```

**Multi-project support**

It is normal practice to have layered architecture and hence model classes could be in different project. Please refer [this](http://stackoverflow.com/a/21895258/309395) Stackoverflow post for the same. I have modified it a bit to generate the help documentation, please follow the steps mentioned below:

1. Enable XML documentation for your subproject (from project properties / build) like you have for your Web API project. Except this time, route it directly to `projectname.XmlDocument.xml` so that it gets generated in your project root folder. Replace projectname with name of your project.

2. Modify your Web API project postbuild event to copy this XML file into your `App_Data` folder:

```batch
copy "$(SolutionDir)SubProject\XmlDocument.xml" "$(ProjectDir)\App_Data\Subproject.xml"
```

Note: Paths are wrapped in double quotes to handle space in between path.

3. Next open `Areas\HelpPage\App_Start\HelpPageConfig` and locate the following line:

```csharp
config.SetDocumentationProvider(new XmlDocumentationProvider(
    HttpContext.Current.Server.MapPath("~/App_Data/XmlDocument.xml")));
```

and replace it with below:

```csharp
config.SetDocumentationProvider(new XmlDocumentationProvider(
    HttpContext.Current.Server.MapPath("~/App_Data")));
```

4. Update `Areas\HelpPage\XmlDocumentationProvider` as shown below:

Replace the `_documentNavigator` field with:

```csharp
private List<XPathNavigator> _documentNavigators = new List<XPathNavigator>();
```

Replace the constructor with:

```csharp
public XmlDocumentationProvider(string appDataPath)
{
    if (appDataPath == null)
    {
        throw new ArgumentNullException("appDataPath");
    }
    foreach (var file in Directory.GetFiles(appDataPath, "*.xml"))
    {
        XPathDocument xpath = new XPathDocument(Path.Combine(appDataPath, file));
        _documentNavigators.Add(xpath.CreateNavigator());
    }
}
```

Note: Above constructor is different than the original stackoverflow post. Here we are not defining list of files, but will consider all the files having .xml extension.

Add the following method below the constructor:

```csharp
private XPathNavigator SelectSingleNode(string selectExpression)
{
    foreach (var navigator in _documentNavigators)
    {
        var propertyNode = navigator.SelectSingleNode(selectExpression);
        if (propertyNode != null)
            return propertyNode;
    }
    return null;
}
```

Fix all compiler errors (there should be three) resulting in references to `_documentNavigator.SelectSingleNode` and remove the `_documentNavigator.` portion so that it now calls the new `SelectSingleNode` method we defined above.

For advanced customization, please refer [this](https://blogs.msdn.microsoft.com/yaohuang1/2012/09/30/asp-net-web-api-help-page-part-1-basic-help-page-customizations/). I hope it helps!
