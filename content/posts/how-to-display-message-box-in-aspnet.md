---
title: 'How to display message box in Asp.net?'
date: 2012-01-02T22:34:00.000+05:30
draft: false
url: /2012/01/how-to-display-message-box-in-aspnet.html
tags: 
- JavaScript
- jQuery
- Asp.net
---

Every new beginner from windows form development background uses MsgBox.Show method but unfortunately it executes server side and will display the message box on server. Then they ask it on different forums seeking help, how to display message box in Asp.net then? (one of the most frequent and common question on asp.net forum)  
There are various ways to do it.  

*   Use JavaScript alert to display messagebox, confirm and prompt
*   Download [alert](http://alerts.codeplex.com/) control from codeplex and use it.
*   Use Modal Popup or Confirm Button from [Ajax Toolkit](http://www.asp.net/ajaxLibrary/AjaxControlToolkitSampleSite/Default.aspx)
*   Use [Jquery UI Dialog](http://jqueryui.com/demos/dialog/)

You can use any Jquery based dialog box. I’ve been using [Jalert](http://abeautifulsite.net/blog/2008/12/jquery-alert-dialogs/) for quite a long time. It is easy to use and you can quickly customize as per the need. One of the best part of Jalert is that it allows you to perform certain activity by using Callback function. Before using Jalert, make sure you’ve referenced [Jquery](http://jquery.com/) in your project.  
  
Note: Jalert is no longer actively maintained and they recommend to use Jquery UI Dialog.  
  
Now, how do you display it from server side?  
  
You can use following code in your class file under App\_Code folder or You can include it in Base Class, being one of the useful feature as suggested by Scott Mitchell [here](http://dotnetslackers.com/articles/aspnet/Four-Helpful-Features-to-Add-to-Your-Base-Page-Class.aspx)  
  
```
public enum EnmMsgType
{
      ALERT,
      PROMPT,
      SUCCESS,
      ERROR,
      WARNING
}
```  
```
/// <summary>
/// Displays Message box 
/// </summary>
/// <param name="strMsg">Message to be displayed</param>
/// <param name="strTitle">Title of the message</param>
/// <param name="strMsgType">Type of the message to be displayed</param>
/// <param name="strCallBack">Function to be executed after message has been displayed</param>
public static void MsgBoxAlert(string strMsg, string strTitle, EnmMsgType strMsgType, string strCallBack)
{
    Page mypage = default(Page);
    string strScript = string.Empty;
   
    try
    {
        string cleanMessage = strMsg.Replace("'", "\\\\'");

        strScript = GetMessageBoxScript(strTitle, strCallBack, strMsgType, cleanMessage);

        //Gets currently executing page
        mypage = HttpContext.Current.CurrentHandler as Page;
        // Checks if the handler is a Page and that 
        //if the startup script is already registered.
        if (mypage != null && !mypage.ClientScript.IsStartupScriptRegistered("alert"))
        {
            mypage.ClientScript.RegisterStartupScript(mypage.GetType(), "alert", strScript);
        }
    }
    catch (Exception ex)
    {
      //Your exception handling code here
    }
}
```  
You can name function whatever you want. As shown in code strCallBack parameter accepts name of the function which will be called in case you want to perform certain action after Messagebox or Confirmation dialog has been displayed to user and user presses Ok button. If you don’t want to call any function, just pass an empty string to function in above code.  
```
private static string GetMessageBoxScript(string strTitle, string strCallBack, EnmMsgType MsgType, string cleanMessage)
{
    string strClass =  GetMessageboxClass(MsgType);
    StringBuilder sbMessageScript = new StringBuilder();
    sbMessageScript.Append("<script type='text/javascript'>");
    sbMessageScript.Append("$(document).ready(function(){");
    sbMessageScript.Append("jAlert('");
    sbMessageScript.Append(cleanMessage);
    sbMessageScript.Append("','");
    sbMessageScript.Append(strClass);
    sbMessageScript.Append("','");
    sbMessageScript.Append(strTitle);
    sbMessageScript.Append("'");
    if (strCallBack != string.Empty)
    {
        sbMessageScript.Append(",");
        sbMessageScript.Append(strCallBack);
    }
    sbMessageScript.Append(");});");
    sbMessageScript.Append("</script>");

    return sbMessageScript.ToString();
}
```  
Below function will return appropriate class based on Message Type being passed. You have to have those classes added in your CSS file. As per the class respective image will be displayed in message box, just like windows message box.  
  
```
/// <summary>
/// Gets the messagebox class.
/// </summary>
/// <param name="strMsgType">Type of the Message</param>
/// <returns></returns>
private static string GetMessageboxClass(EnmMsgType strMsgType)
{
    string strClass = String.Empty;

    if (strMsgType == EnmMsgType.ALERT)
    {
        strClass = "alert";
    }
    else if (strMsgType == EnmMsgType.SUCCESS)
    {
        strClass = "success";
    }
    else if (strMsgType == EnmMsgType.ERROR)
    {
        strClass = "error";
    }
    else if (strMsgType == EnmMsgType.WARNING)
    {
        strClass = "warning";
    }
    return strClass;
}
```  
Check out the below example :  
  
![jAlert](/images/5369a-jalert_thumb25255b525255d.jpg)  
  
I hope it will be helpful to every beginner from Windows Form background. If you have any query/suggestion, please comment. :)