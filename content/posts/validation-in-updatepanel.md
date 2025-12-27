---
title: "Validation in Updatepanel"
description: "How to use validation controls with ASP.NET UpdatePanel control."
summary: "How to use validation controls with ASP.NET UpdatePanel control."
date: 2008-10-13T14:42:00.000+05:30
draft: false
url: /2008/10/validation-in-updatepanel.html
tags: 
- Updatepanel
- Asp.net Ajax
---

I am writing this post about how to use validation controls with asp.net updatepanel control after seeing lots of posts in asp.net forums, Orkut communities of asp.net that their validation controls do not work inside update panel.

To resolve this error, add the tagmapping section in web.config file of your project like this:

```xml
<tagMapping>
  <add tagType="System.Web.UI.WebControls.CompareValidator"
       mappedTagType="Sample.Web.UI.Compatibility.CompareValidator, Validators, Version=1.0.0.0"/>
  <add tagType="System.Web.UI.WebControls.CustomValidator"
       mappedTagType="Sample.Web.UI.Compatibility.CustomValidator, Validators, Version=1.0.0.0"/>
  <add tagType="System.Web.UI.WebControls.RangeValidator"
       mappedTagType="Sample.Web.UI.Compatibility.RangeValidator, Validators, Version=1.0.0.0"/>
  <add tagType="System.Web.UI.WebControls.RegularExpressionValidator"
       mappedTagType="Sample.Web.UI.Compatibility.RegularExpressionValidator, Validators, Version=1.0.0.0"/>
  <add tagType="System.Web.UI.WebControls.RequiredFieldValidator"
       mappedTagType="Sample.Web.UI.Compatibility.RequiredFieldValidator, Validators, Version=1.0.0.0"/>
  <add tagType="System.Web.UI.WebControls.ValidationSummary"
       mappedTagType="Sample.Web.UI.Compatibility.ValidationSummary, Validators, Version=1.0.0.0"/>
</tagMapping>
```

For more information you can refer Scottgu's post [Common Gotcha: Validation Controls used within the control](http://weblogs.asp.net/scottgu/archive/2007/01/25/links-to-asp-net-ajax-1-0-resources-and-answers-to-some-common-questions.aspx) and Matt Gibbs post [ASP.NET AJAX Validators](http://blogs.msdn.com/mattgi/archive/2007/01/23/asp-net-ajax-validators.aspx).

Happy Programming.. ;)
