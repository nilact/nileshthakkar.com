---
title: 'Jquery DataTables : Add column filter and remove global filter'
date: 2014-01-15T15:50:00.000+05:30
draft: false
url: /2014/01/jquery-datatables-add-column-filter-and.html
tags: 
- jQuery
---

Jquery [DataTables](http://datatables.net/) plugin allows you to search through all the columns and it works pretty well. Now if you wan to add column wise filter, you need to use [JQuery DataTables Column Filter plugin](https://code.google.com/p/jquery-datatables-column-filter/).  
  
To extend Jquery DataTables with Column Filter plugin, you need to have code like below:  

```
     <asp:Repeater ID="repUserTempAssignment" runat="server" DataSourceID="SqlDataSource1"
            EnableViewState="false">
            <HeaderTemplate>
                <table class="table-data" id="tblEmployee" style="width: 100%px;">
                    <thead>
                        <tr>
                            <th>
                                First Name
                            </th>
                            <th>
                                Last Name
                            </th>
                            <th>
                                City
                            </th>
                            <th>
                                Country
                            </th>
                        </tr>
                        <tr>
                            <th>
                                First Name
                            </th>
                            <th>
                                Last Name
                            </th>
                            <th>
                                City
                            </th>
                            <th>
                                Country
                            </th>
                        </tr>
                    </thead>
                    <tbody>
            </HeaderTemplate>
            <ItemTemplate>
                <tr>
                    <td>
                        <%# Eval("FirstName") %>
                    </td>
                    <td>
                        <%# Eval("LastName") %>
                    </td>
                    <td>
                        <%# Eval("City") %>
                    </td>
                    <td>
                        <%# Eval("Country") %>
                    </td>
                </tr>
            </ItemTemplate>
            <FooterTemplate>
                </tbody><tfoot>
                </tfoot>
                </table>
            </FooterTemplate>
        </asp:Repeater>
```

I’m using asp.net repeater control to bind employee list. If you notice in above code table has two rows in header. One will work as table header and one will be replaced with textboxes at runtime which will work as column filter. For that you need below script:  

```
$('#tblEmployee').dataTable(
            {
                "bSortCellsTop": true
            }).columnFilter(
            {
                sPlaceHolder: "head:after"
            });
```

[bSortCellsTop](http://datatables.net/ref#bSortCellsTop): This will apply sorting on first row of header.  
[sPlaceHolder](http://jquery-datatables-column-filter.googlecode.com/svn/trunk/dateRange.html): It will add textboxes in second row of header for every column. If you omit this, there won’t be any filters applied to columns.  
  
After adding above code, table will look like below:  
  
![image](/images/b8eda-image_thumb25255b725255d.png)  
  
Now to get rid of global search filter you need to add below code in dataTable settings:  
  

```
"sDom": '<"top"l>rt<"bottom"ip><"clear">'
```

  
[sDom](http://datatables.net/ref#sDom): It allow you to specify exactly where in the DOM you want DataTables to inject the various controls it adds to the page.  
  
If you’ve not looked into documentation and if you google for quick solution, you might find may solutions stating to use [bFilter](http://datatables.net/ref#bfilter), but it simply enable/disable filtering capability of dataTable. If one doesn’t want to use default styling of dataTable and want to apply his own classes, [bSortClasses](http://datatables.net/ref#bSortClasses) needs to be used. Final code would be:  
  

```
$(document).ready(function () {
            $('#tblEmployee').dataTable(
            {
                "bSortCellsTop": true,
                "sDom": '<"top"l>rt<"bottom"ip><"clear">',
                "bFilter": false,
                "bSortClasses": false
            }).columnFilter(
            {
                sPlaceHolder: "head:after"
            });
        });
```

  
Using above code, global filter won’t be added to page as shown below:  
  
![image](/images/9a370-image25255b1125255d.png)  
  
I hope it helps!