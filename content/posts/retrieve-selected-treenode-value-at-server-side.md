---
title: 'Retrieve selected treenode value at server side'
date: 2009-05-05T23:28:00.003+05:30
draft: false
url: /2009/05/retrieve-selected-treenode-value-at.html
tags: 
- TreeView
- VS2005
- Asp.net
---

As I said in my previous post here’s the way to retrieve selected tree node value at the server-side.

![treeviewcheckbox](/images/8889f-treeviewcheckbox_thumb.jpg)

In the above image, all the nodes are selected including parent node. In my first [post](http://nilthakkar.blogspot.com/2009/03/populate-treeview-with-database-fields.html) we concatenated the value of the parent node with child node and assigned it to child node so that whenever we want to retrieve value of child node, we can have parent node value.

Here’s the output of the selected nodes:

![data](/images/bbee3-data_thumb.jpg)

**C#**

```csharp
protected void btnGetNode_Click(object sender, EventArgs e)
{
    TreeNodeCollection tn = tvSample.CheckedNodes;
    for (int i = 0; i < tn.Count; i++)
    {
        String[] arr =  tn[i].Value.Split('$');
        if (arr.Length == 2)
        {
            Response.Write("<br>Parent Node Value" + arr[1] + "<br>");
            Response.Write("<br>Child Node: " + tn[i].Text + " Value: " + arr[0]);
        }
    }
}
```

**VB**

```vb
Protected Sub btnGetNode_Click(ByVal sender As Object, ByVal e As EventArgs) Handles btnGetNode.Click
    Dim tn As TreeNodeCollection = tvSample.CheckedNodes
    For i As Integer = 0 To tn.Count - 1
        Dim arr As [String]() = tn(i).Value.Split("$"c)
        If arr.Length = 2 Then
            Response.Write("<br>Parent Node Value" & arr(1) & "<br>")
            Response.Write("<br>Child Node: " & tn(i).Text & " Value: " + arr(0))
        End If
    Next
End Sub
```

As shown in the above code, we are splitting node value with the ‘**$’** sign. The first value is parent node value and the second value is child node value. Another way is to loop through all the nodes and check for the checked node value.

I hope this helps!! :) 

In my next post, I will write about **check tree nodes on the basis of database value.**