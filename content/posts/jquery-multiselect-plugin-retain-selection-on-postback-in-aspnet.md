---
title: 'Jquery Multiselect plugin retain selection on postback in Asp.net'
date: 2013-05-11T19:30:00.001+05:30
draft: false
url: /2013/05/jquery-multiselect-plugin-retain.html
tags: 
- JavaScript
- jQuery
- Asp.net
---

One can use [Jquery Mutiselect](http://www.erichynds.com/blog/jquery-ui-multiselect-widget) plugin to allow user to select multiple items from the options available in dropdown. When we click on dropdown it looks something like below:  
  
![image](/images/73080-image_thumb.png)  
Check out the demo page of plugin [here](http://www.erichynds.com/examples/jquery-ui-multiselect-widget/demos/index.htm#basic)  
  
The problem with the plugin is that it won’t remember the user selection and value will be lost on postback. For that you can store the selected valued delimited by comma in hidden field and restore the value on page load.  
  
Now let’s look at the code to bind the dropdown with plugin and retaining value on postback.  
Make sure you’ve necessary scripts and css files added in your page:  

```
  <link href="CSS/Jquery.multiselect.css" rel="stylesheet" type="text/css" />
  <link href="CSS/Jquery.multiselect.filter.css" rel="stylesheet" type="text/css" />
  <link href="CSS/jquery-ui.css" rel="stylesheet" type="text/css" />
  <script src="http://code.jquery.com/jquery-1.9.1.min.js" type="text/javascript"></script>
  <script type="text/javascript" src="Js/jquery-ui.min.js"></script>
  <script type="text/javascript" src="Js/Jquery.multiselect.js"></script>
  <script src="JS/Jquery.multiselect.filter.js" type="text/javascript"></script>
```

**HTML:**  

```
<asp:DropDownList ID="ddlUsers" runat="server" multiple="multiple">
 </asp:DropDownList>
 <asp:HiddenField ID="hdnUsers" runat="server" />
 <asp:Button ID="btnSearch" runat="server" Text="Search"  onclick="btnSearch\_Click" />
```

**Client Script:**  

```
<script type="text/javascript">
        $('#<%= ddlUsers.ClientID %>').multiselect({
            //to remove check/uncheck all option
            header: '',
            //store user selected value in hidden field
            close: function (event, ui) {
                var array\_of\_SalesGroup = $('#<%= ddlUsers.ClientID %>').multiselect("getChecked").map(function () {
                    return this.value;
                }).get();
                $('#<%= hdnUsers.ClientID %>').val(array\_of\_SalesGroup);
            }
        }).multiselectfilter(); //adds filter to dropdown
    </script>
```

  
Now to check checkboxes marked by user we need to add selected attribute to original select (dropdown) options and call refresh method of the plugin. You can do it as shown in below scripts:  
  

```
        $(document).ready(function () {
            if ($('#<%= hdnUsers.ClientID %>').val() != '') {
                var selected = $('#<%= hdnUsers.ClientID %>').val().split(",");
                $("#<%=ddlUsers.ClientID%> > option").each(function () {
                    if ($.inArray(this.value, selected) > -1) {
                            $(this).attr("selected", "selected");
                    }
                });
               $("#<%=ddlUsers.ClientID%>").multiselect('refresh');
            }
        });
```

  
I hope it helps!