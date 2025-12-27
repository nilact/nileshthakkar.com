---
title: 'JQuery UI AutoComplete TextBox from Database using PageMethod in ASP.Net'
date: 2014-01-16T16:25:00.000+05:30
draft: false
url: /2014/01/jquery-ui-autocomplete-textbox-from.html
tags: 
- JavaScript
- Asp.net Ajax
- Jquery UI
- jQuery
- Asp.net
---

In this post we’ll see how to user Jquery UI Autocomplete with asp.net pagemethod.  
  
For database, we’ll be using [Northwind](http://northwinddatabase.codeplex.com/releases/view/71634) database.  
  
**PageMethod:**  

```
\[ScriptMethod(ResponseFormat= ResponseFormat.Json)\]
    \[WebMethod\]
    public static List<AutoCompleteHelper> GetEmployee(string searchText)
    {
        using (SqlConnection conn = new SqlConnection())
        {
            conn.ConnectionString = ConfigurationManager
                    .ConnectionStrings\["NorthwindConnectionString"\].ConnectionString;
            using (SqlCommand cmd = new SqlCommand())
            {
                cmd.CommandText = "select CustomerID,ContactName from Customers where " +
                "ContactName like @SearchText + '%'";
                cmd.Parameters.AddWithValue("@SearchText", searchText);
                cmd.Connection = conn;
                List<AutoCompleteHelper> listAutoCompleteHelper = new List<AutoCompleteHelper>();
                conn.Open();
                using (SqlDataReader sdr = cmd.ExecuteReader())
                {
                    while (sdr.Read())
                    {
                        AutoCompleteHelper objCompleteHelper = new AutoCompleteHelper();
                        objCompleteHelper.displayText = Convert.ToString(sdr\["ContactName"\]);
                        objCompleteHelper.displayValue = Convert.ToString(sdr\["CustomerID"\]);
                        listAutoCompleteHelper.Add(objCompleteHelper);
                        
                    }
                }
                conn.Close();
                Thread.Sleep(3000);
                return listAutoCompleteHelper;
            }
        }
    }
```

In above code, we’re fetching data from customer table from northwind database based on text entered by user. You may want to have this code in separate (data access) layer and must use stored procedure rather than inline query, I’ve included this code just for demonstration purpose.  
  
Select query will fetch CustomerId and CustomerName based on user input.  
  
You may want to omit Thread.Sleep(3000), here it is used to delay response so that loading image can be displayed in textbox.  
  
Pagemethod returns list of AutocompleteHelper class which is as below:  

```
public class AutoCompleteHelper
{
    public string displayText { get; set; }
    public string displayValue { get; set; }
}
```

**JavaScript:**  

```
$(document).ready(function () {
            $('#<%=txtEmployeeSearch.ClientID %>').autocomplete({
                source: function (request, response) {
                    $.ajax({
                        type: "POST",
                        url: '<%=ResolveUrl("~/JqueryAutocomplete.aspx/GetEmployee")%>',
                        data: "{'searchText': '" + request.term + "'}",
                        contentType: "application/json; charset=utf-8",
                        dataType: "json",
                        success: function (dataJson) {
                            var data = dataJson.hasOwnProperty("d") ? dataJson.d : dataJson;
                            response(
                                    $.map(data, function (item) {
                                        return {
                                            label: item.displayText,
                                            value: item.displayValue

                                        }
                                    }));
                        },
                        failure: function (data) {
                            alert(data);
                        }
                    });
                },
                minLength: 1,
                search: function (e, u) {
                    $(this).addClass('ajaxprocessing');
                },
                response: function( event, ui ) {
                    $(this).removeClass('ajaxprocessing');
                },
                select: function (event, ui) {
                    event.preventDefault();
                    $('#<%= hdnEmployeeId.ClientID %>').val(ui.item.value);
                    this.value = ui.item.label;
                    return false;
                },
                focus: function (event, ui) {
                    event.preventDefault();
                    this.value = ui.item.label;
                }
            });
        });
```

We’re using [search](http://api.jqueryui.com/autocomplete/#event-search) and [response](http://api.jqueryui.com/autocomplete/#event-response) events of Jquery UI to add and remove **ajaxprocessing** class respectively to show loading image. In [select](http://api.jqueryui.com/autocomplete/#event-select) event we’re storing value(customer Id) in hiddenfield.  
In success callback function, we’ve below line:  

```
 var data = dataJson.hasOwnProperty("d") ? dataJson.d : dataJson;
```

Which checks whether response is surrounded by d attribute. (Ajax response contains .d attribute, if you’re using .net 3.5+, but in asp.net 2.0, it simply returns response. For more info, check [this](http://encosia.com/a-breaking-change-between-versions-of-aspnet-ajax/) post of Dave Ward.)  
  
**HTML:**  

```
 <asp:TextBox ID="txtEmployeeSearch" runat="server"></asp:TextBox>
 <asp:HiddenField ID="hdnEmployeeId" runat="server" />
```

**CSS:**  

```
 .ajaxprocessing
    {
        background: url('Content/Images/ajax-loader.gif');
        background-repeat: no-repeat;
        background-position: right;
    }
```

Now run your page, and it should be working as shown in below image:  
  
![Jquery UI Autocomplete using pagemethods](/images/b2c82-jquery252520ui252520autocomplete252520using252520pagemethods_thumb25255b725255d.gif)  
  
Notes:  
  

*   If your pagemethod is not being hit and your ajax response contains whole page, then make sure you’ve added necessary configuration entries to make pagemethod work. Please add settings mentioned [here](http://www.asp.net/AJAX/Documentation/Live/ConfiguringASPNETAJAX.aspx).
*   IE10 may not pass parameter for Ajax Post, and hence it may throw “Invalid web service call, missing value for parameter” error. To solve the error, you need to add below meta tag in page head:

  

```
<meta http-equiv="x-ua-compatible" content="IE=9" >
```

  
I hope it helps!