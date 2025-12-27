---
title: "Show Modal Dialog with Asp.net webpage"
description: "How to display Modal Dialog with ASP.NET webpage using JavaScript ShowModalDialog method."
summary: "How to display Modal Dialog with ASP.NET webpage using JavaScript ShowModalDialog method."
date: 2009-10-02T22:55:00.007+05:30
draft: false
url: /2009/10/show-modal-dialog-with-aspnet-webpage.html
tags: 
- JavaScript
- Asp.net
---

For last few days one question is arising on asp.net forums and that is how to display Modal Dialog with Asp.net webpage. It can be done using JavaScript ShowModalDialog method.

**Syntax:**

```javascript
returnVal = window.showModalDialog(uri[, arguments][, options]);
```

Where,

*   uri is the uri of the document to be displayed in the dialog box.
*   `arguments` is an optional variant that contains values that should be passed to the dialog box; these are made available in the window object's `window.dialogArguments` property.
*   options an optional string that specifies different attributes separated by semicolon that will display modal dialog accordingly.

This method is introduced by Microsoft Internet Explorer 4 and support added to Firefox 3. Firefox 3 does not support `dialogHide`, `edge`, `status`, or `unadorned` arguments.

Here is the code that is reusable. You can have following code in your class file as well and it works like a charm, once you add the namespace under which it is declared ;)

**VB**

```vb
Public Shared Sub OpenWindow(ByVal currentPage As Page, ByVal poppage As String, ByVal width As Int32, ByVal height As Int32)
    Dim sb As New System.Text.StringBuilder()
    sb.Append("var features;")
    sb.Append("features='dialogwidth:" & width & "px;dialogheight:" & height & "px;toolbar:0;scrollbars:0;location:0;statusbar:0;menubar:0;resizable:0';")
    sb.Append("popWin=window.showModalDialog('")
    sb.Append(poppage)
    sb.Append("','")
    sb.Append("',")
    sb.Append("features")
    sb.Append(");")
    'this will reload the parent page once modal dialog is closed
    sb.Append("__doPostBack('ReturnFromDialogPostBack',popWin);")
    ScriptManager.RegisterStartupScript(currentPage, currentPage.GetType(), "OpenWindow", sb.ToString(), True)
End Sub
```

**C#**

```csharp
public static void OpenWindow(Page currentPage, string poppage, Int32 width, Int32 height)
{
    System.Text.StringBuilder sb = new System.Text.StringBuilder();
    sb.Append("var features;");
    sb.Append("features='dialogwidth:" + width + "px;dialogheight:" + height + "px;toolbar:0;scrollbars:0;location:0;statusbar:0;menubar:0;resizable:0';");
    sb.Append("popWin=window.showModalDialog('");
    sb.Append(poppage);
    sb.Append("','");
    sb.Append("',");
    sb.Append("features");
    sb.Append(");");
    sb.Append("__doPostBack('ReturnFromDialogPostBack',popWin);");
    ScriptManager.RegisterStartupScript(currentPage, currentPage.GetType(), "OpenWindow", sb.ToString(), true);
}
```

Add following tag in head tag of your modal dialog page (Popup page):

```html
<base target="_self" />
```

It will prevent popup page to open new window.

Add below line of code if you want to perform some server side processing or add it in JavaScript function:

```vb
Response.Write("<script language='JavaScript' type='text/JavaScript'>window.close();</script>")
```

I hope this helps!!
