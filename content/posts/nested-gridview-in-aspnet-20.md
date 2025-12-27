---
title: "Nested Gridview in Asp.net 2.0"
description: "How to create nested GridView in ASP.NET 2.0 with expand/collapse functionality."
summary: "How to create nested GridView in ASP.NET 2.0 with expand/collapse functionality."
date: 2009-05-18T23:53:00.002+05:30
draft: false
url: /2009/05/nested-gridview-in-aspnet-20.html
tags: 
- JavaScript
- VS2005
- GridView
- Asp.net
---

As i said in my last post that i will write about Check/ Uncheck checkboxes of Treeview on the basis of database value and i think it is very easy so skipping that part and here i am going to write about Nested Gridview that will look like below image:

![Nested Gridview](/images/2b81b-nestedgridview_thumb6.jpg)

Rather than displaying Nested Gridview in the same row, we will display it in below row that gives user proper understanding of the data. Let us see, How it can be done?

First of all i am using Northwind Database that comes with SQL Server and if you are not having it, you can download it.

Here is the HTML Source Code for Parent GridView, change it according the language C# or VB and your requirement:

```html
<asp:GridView ID="gvOrders" runat="server" AutoGenerateColumns="False" 
    DataSourceID="SqlDataSource1" DataKeyNames="OrderID" CellPadding="4" ForeColor="#333333">
  <Columns>
    <asp:TemplateField>
      <ItemTemplate>
        <asp:Label ID="lblSign" runat="server" />
      </ItemTemplate>
    </asp:TemplateField>
    <asp:BoundField DataField="OrderID" HeaderText="OrderID" InsertVisible="False" ReadOnly="True" SortExpression="OrderID" />
    <asp:BoundField DataField="CustomerID" HeaderText="CustomerID" SortExpression="CustomerID" />
    <asp:BoundField DataField="EmployeeID" HeaderText="EmployeeID" SortExpression="EmployeeID" />
    <asp:BoundField DataField="Name" HeaderText="Name" SortExpression="Name" />
    <asp:BoundField DataField="Address" HeaderText="Address" SortExpression="Address" />
    <asp:BoundField DataField="City" HeaderText="City" SortExpression="City" />
    <asp:BoundField DataField="Region" HeaderText="Region" SortExpression="Region" />
    <asp:BoundField DataField="Postal" HeaderText="Postal" SortExpression="Postal" />
    <asp:BoundField DataField="Country" HeaderText="Country" SortExpression="Country" />
    <asp:TemplateField>
      <ItemTemplate>
        <tr>
          <td colspan="100%">
            <div id="div<%#Eval("OrderID") %>" style="display: none; position: relative;">
            </div>
          </td>
        </tr>
      </ItemTemplate>
    </asp:TemplateField>
  </Columns>
  <RowStyle BackColor="#F7F6F3" ForeColor="#333333" />
  <FooterStyle BackColor="#5D7B9D" Font-Bold="True" ForeColor="White" />
  <PagerStyle BackColor="#284775" ForeColor="White" HorizontalAlign="Center" />
  <SelectedRowStyle BackColor="#E2DED6" Font-Bold="True" ForeColor="#333333" />
  <HeaderStyle BackColor="#5D7B9D" Font-Bold="True" ForeColor="White" />
  <EditRowStyle BackColor="#999999" />
  <AlternatingRowStyle BackColor="White" ForeColor="#284775" />
</asp:GridView>
```

In above code we have inserted tr, td and div tag that is highlighted by Yellow background colour. Gridview is rendered as HTML table and GridView rows will be rendered as tr and cells will be as td so that we are explicitly inserting those tags to show our child Gridview exactly below to the corresponding Gridview Row rather than in last column of Parent Gridview.

SQL DataSource Code for Parent GridView:

```html
<asp:SqlDataSource ID="SqlDataSource1" runat="server"
    SelectCommand="SELECT TOP (10) OrderID, CustomerID, EmployeeID, ShipName as Name,
        ShipAddress as Address, ShipCity as City, ShipRegion as Region, ShipPostalCode as Postal,
        ShipCountry as Country FROM Orders"
    ConnectionString="<%$ ConnectionStrings:NorthwindConnectionString %>">
</asp:SqlDataSource>
```

Child GridView Code:

```html
<asp:GridView ID="gvDetails" runat="server" CellPadding="4" ForeColor="#333333" GridLines="Both"
    DataSourceID="SqlDataSource2" Style="position: relative" AutoGenerateColumns="False">
  <RowStyle BackColor="#F7F6F3" ForeColor="#333333" />
  <FooterStyle BackColor="#5D7B9D" Font-Bold="True" ForeColor="White" />
  <PagerStyle BackColor="#284775" ForeColor="White" HorizontalAlign="Center" />
  <SelectedRowStyle BackColor="#E2DED6" Font-Bold="True" ForeColor="#333333" />
  <HeaderStyle BackColor="#5D7B9D" Font-Bold="True" ForeColor="White" />
  <EditRowStyle BackColor="#999999" />
  <AlternatingRowStyle BackColor="White" ForeColor="#284775" />
  <Columns>
    <asp:BoundField DataField="FirstName" HeaderText="FirstName" SortExpression="FirstName" />
    <asp:BoundField DataField="LastName" HeaderText="LastName" SortExpression="LastName" />
    <asp:BoundField DataField="UnitPrice" HeaderText="UnitPrice" SortExpression="UnitPrice" />
    <asp:BoundField DataField="Quantity" HeaderText="Quantity" SortExpression="Quantity" />
    <asp:BoundField DataField="Discount" HeaderText="Discount" SortExpression="Discount" />
    <asp:BoundField DataField="CompanyName" HeaderText="CompanyName" SortExpression="CompanyName" />
  </Columns>
</asp:GridView>
```

SQL DataSource Code for Child GridView:

```html
<asp:SqlDataSource ID="SqlDataSource2" runat="server" 
    ConnectionString="<%$ ConnectionStrings:NorthwindConnectionString %>"
    SelectCommand="SELECT C.CompanyName, OD.UnitPrice, OD.Quantity, OD.Discount, E.FirstName, E.LastName, 
        OD.OrderID, C.CustomerID, E.EmployeeID 
        FROM dbo.[Order Details] AS OD 
        INNER JOIN dbo.Orders AS O ON OD.OrderID = O.OrderID 
        INNER JOIN dbo.Customers AS C ON O.CustomerID = C.CustomerID 
        INNER JOIN dbo.Employees AS E ON O.EmployeeID = E.EmployeeID 
        AND O.OrderID LIKE @OrderId AND C.CustomerID = @CustomerID AND E.EmployeeID = @EmployeeId">
  <SelectParameters>
    <asp:Parameter Name="OrderId" />
    <asp:Parameter Name="CustomerId" />
    <asp:Parameter Name="EmployeeId" />
  </SelectParameters>
</asp:SqlDataSource>
```

We will supply OrderId, CustomerID, EmployeeID to SQL DataSource of ChildGridView in RowDataBound of Parent GridView.

**Parent GridView Event Handlers**

**C#**

```csharp
protected void gvOrders_RowCreated(object sender, GridViewRowEventArgs e)
{
    if (e.Row.RowType == DataControlRowType.Header || e.Row.RowType == DataControlRowType.DataRow)
    {
        e.Row.Cells[1].Visible = false;
        e.Row.Cells[2].Visible = false;
        e.Row.Cells[3].Visible = false;
    }
}
```

**VB**

```vb
Protected Sub gvOrders_RowCreated(ByVal sender As Object, ByVal e As System.Web.UI.WebControls.GridViewRowEventArgs) Handles gvOrders.RowCreated
    If e.Row.RowType = DataControlRowType.Header Or e.Row.RowType = DataControlRowType.DataRow Then
        e.Row.Cells(1).Visible = False
        e.Row.Cells(2).Visible = False
        e.Row.Cells(3).Visible = False
    End If
End Sub
```

**C#**

```csharp
protected void gvOrders_RowDataBound(object sender, GridViewRowEventArgs e)
{
    if (e.Row.RowType == DataControlRowType.Header)
    {
        e.Row.Cells[1].Visible = false;
        e.Row.Cells[2].Visible = false;
        e.Row.Cells[3].Visible = false;
    }
    if (e.Row.RowType == DataControlRowType.DataRow)
    {
        SqlDataSource s = (SqlDataSource)e.Row.FindControl("SqlDataSource2");
        s.SelectParameters[0].DefaultValue = e.Row.Cells[1].Text;
        s.SelectParameters[1].DefaultValue = e.Row.Cells[2].Text;
        s.SelectParameters[2].DefaultValue = e.Row.Cells[3].Text;

        ((Label)e.Row.FindControl("lblSign")).Text = "+";
        ((Label)e.Row.FindControl("lblSign")).Attributes["onclick"] = "HideShowDiv('div" + e.Row.Cells[1].Text + "', '" + ((Label)e.Row.FindControl("lblSign")).ClientID + "');";
        e.Row.Cells[0].Attributes["onmouseover"] = "this.style.cursor='pointer'";
        e.Row.Cells[0].Attributes["onmouseout"] = "this.style.cursor='pointer'";
    }
}
```

**JavaScript**

```javascript
function HideShowDiv(DivId, LblSign) {
    var mydiv = document.getElementById(DivId);
    var LblSign = document.getElementById(LblSign);
    if (mydiv != null && LblSign != null) {
        if (mydiv.style.display != "block") {
            mydiv.style.display = "block";
            LblSign.innerHTML = "-";
        }
        else {
            mydiv.style.display = "none";
            LblSign.innerHTML = "+";
        }
    }
}
```

Above JavaScript function will Hide/Show Div and changes Sign of Parent Row on expansion of Div and so on. I hope this helps!! :)

Next Time i will write "How to generate Child Gridview Dynamically" :)

Enjoy!!
