---
title: 'Populate Treeview With Database Fields'
date: 2009-03-26T22:04:00.017+05:30
draft: false
url: /2009/03/populate-treeview-with-database-fields.html
tags: 
- TreeView
- VS2005
- Asp.net
---

This post describes how to populate treeview with database fields. People often required to populate treeview with database fields and they choose different approaches to make it work. Here I am going to describe the simplest way.  

  
Here I'll be using Sql Server Sample Database: Pubs  
  

Relations:  
  
[![](http://4.bp.blogspot.com/_2cveClWhO_o/Scu3_CDa48I/AAAAAAAAAKc/SvxkVDaR46U/s400/Relation.JPG)](http://4.bp.blogspot.com/_2cveClWhO_o/Scu3_CDa48I/AAAAAAAAAKc/SvxkVDaR46U/s1600-h/Relation.JPG)Treeview :  
  
[![](http://4.bp.blogspot.com/_2cveClWhO_o/Scu5cZG5VAI/AAAAAAAAAKk/o4p8h6U4HDw/s400/TreeView.JPG)](http://4.bp.blogspot.com/_2cveClWhO_o/Scu5cZG5VAI/AAAAAAAAAKk/o4p8h6U4HDw/s1600-h/TreeView.JPG)  
VB Code :

```vb
Public Sub GenerateTreeView()
   Dim ds As New DataSet
   Dim conn As New SqlConnection
   Dim connString As String
   Dim strQuery As String
   Dim da As SqlDataAdapter
   Dim dtJob As New DataTable
   Dim dtEmp As New DataTable
   Dim nodeJob As TreeNode
   Dim nodeEmp As TreeNode

   Try

   connString = ConfigurationManager.ConnectionStrings("pubsConnectionString").ToString()

   strQuery = "select job_id,job_desc from jobs"
   da = New SqlDataAdapter(strQuery, connString)
   da.Fill(dtJob)
   dtJob.TableName = "Job"
   ds.Tables.Add(dtJob)


   strQuery = "SELECT emp_id, fname, lname, job_id FROM employee ORDER BY job_id"
   da = New SqlDataAdapter(strQuery, connString)
   da.Fill(dtEmp)
   dtEmp.TableName = "Employee"
   ds.Tables.Add(dtEmp)

   'define RelationShip
   ds.Relations.Add("JobEmployee", ds.Tables("Job").Columns("job_id"),
 ds.Tables("Employee").Columns("job_id"))

   For Each dr As DataRow In ds.Tables("job").Rows       'Parent Nodes
       nodeJob = New TreeNode()
       nodeJob.Value = dr("job_id")
       nodeJob.Text = dr("job_desc")
       nodeJob.ShowCheckBox = True
       nodeJob.Expanded = False
       tvSample.Nodes.Add(nodeJob)
       For Each drr As DataRow In dr.GetChildRows("JobEmployee")    'Child Nodes
           nodeEmp = New TreeNode()
           nodeEmp.Text = drr("fname") & " " & drr("lname")
           'here concating parent node value with child node value
           'so that it will be easy to get the selected node value
           nodeEmp.Value = drr("emp_id") & "$" & dr("job_id")
           nodeEmp.ShowCheckBox = True
           nodeJob.ChildNodes.Add(nodeEmp)
       Next
   Next

  Catch ex As Exception
    strQuery = Nothing
    da = Nothing
    conn = Nothing
    connString = Nothing
  Finally
 End Try
End Sub
```

C# Code:

```csharp
public void GenerateTreeView()
{
    DataSet ds = new DataSet();
    SqlConnection conn = new SqlConnection();
    string connString = null;
    string strQuery = null;
    SqlDataAdapter da;
    DataTable dtJob = new DataTable();
    DataTable dtEmp = new DataTable();
    TreeNode nodeJob;
    TreeNode nodeEmp;

    try {

        connString =
ConfigurationManager.ConnectionStrings["pubsConnectionString"].ConnectionString.ToString();

        //Job Table Data
        strQuery = "select job_id,job_desc from jobs";
        da = new SqlDataAdapter(strQuery, connString);
        da.Fill(dtJob);
        dtJob.TableName = "Job";
        ds.Tables.Add(dtJob);

        //Employee Table Data
        strQuery = "SELECT emp_id, fname, lname, job_id FROM employee ORDER BY job_id";
        da = new SqlDataAdapter(strQuery, connString);
        da.Fill(dtEmp);
        dtEmp.TableName = "Employee";
        ds.Tables.Add(dtEmp);

        //define RelationShip
        ds.Relations.Add("JobEmployee", ds.Tables["Job"].Columns["job_id"],
ds.Tables["Employee"].Columns["job_id"]);

        foreach (DataRow dr in ds.Tables["job"].Rows) {
            nodeJob = new TreeNode();
            nodeJob.Value = dr["job_id"].ToString();
            nodeJob.Text = dr["job_desc"].ToString();
            nodeJob.ShowCheckBox = true;
            nodeJob.Expanded = false;
            tvSample.Nodes.Add(nodeJob);
            foreach (DataRow drr in dr.GetChildRows("JobEmployee")) {
                nodeEmp = new TreeNode();
                nodeEmp.Text = drr["fname"].ToString() + drr["lname"].ToString();
                nodeEmp.ShowCheckBox = true;
                nodeJob.ChildNodes.Add(nodeEmp);
            }
        }
    }
    catch (Exception ex) {
        strQuery = null;
        da = null;
        conn = null;
        connString = null;
    }
    finally {
    }
}
```

In my next posts i will write about javascript that will be useful to check/ uncheck all the checkboxes of treeview and how to retrieve value of all the checked nodes server side and client side.  

  
I hope this helps :)