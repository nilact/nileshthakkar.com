---
title: "TreeView Node Value at Client Side"
description: "Retrieve selected ASP.NET TreeView node text and value on the client side."
summary: "Retrieve selected ASP.NET TreeView node text and value on the client side."
date: 2009-04-17T22:45:00.004+05:30
draft: false
url: /2009/04/treeview-node-value-at-client-side.html
tags: 
- JavaScript
- TreeView
- Asp.net
---

As I said in my last post, in this post I will demonstrate how to retrieve the value of selected nodes at the client-side. First of all, if you are not aware of how to Populate Treeview dynamically with database fields and check or Uncheck TreeView Checkboxes then please refer respective posts.

Here is the snap of treeview selected nodes:

![treenode](/images/ab265-treenode_thumb.jpg)

As you can see in the above image the **Designer** is the parent node and others are child nodes. Now it is very necessary to understand how this will be rendered so that we can write appropriate ClientScript.

Here is the rendered HTML of treeview node:

```html
<td style="white-space:nowrap;">
    <input type="checkbox" name="tvSamplen54CheckBox" id="tvSamplen54CheckBox" />
    <a class="tvSample_0" href="javascript:__doPostBack('tvSample','s14\\ENL44273F$14')" id="tvSamplet54">ElizabethLincoln</a>
</td>
```

Our area of interest is the anchor tag in the above code. It contains node value in its href attribute. Here is the client script function that is useful to retrieve the value of the selected node at client side.

```javascript
function getNextSibling(element) {
    var n = element;
    do n = n.nextSibling;
    while (n && n.nodeType != 1);
    return n;
}

function GetNodeValue(node) {
    var nodeValue = "";
    var nodePath = node.href.substring(node.href.indexOf(",") + 2, node.href.length - 2);
    var nodeValues = nodePath.split("\\");
    if (nodeValues.length > 1)
        nodeValue = nodeValues[nodeValues.length - 1];
    else
        nodeValue = nodeValues[0].substr(1);
    return nodeValue;
}
```

Here is the function that will call the function in the above code. This function I posted in my previous post but highlighted lines are additional lines.

```javascript
function OnTreeClick(evt) {
  var src = window.event != window.undefined ? window.event.srcElement : evt.target;
  var isChkBoxClick = (src.tagName.toLowerCase() == "input" && src.type == "checkbox");
  if(isChkBoxClick) {
    if (src.checked==true) {
        var nodeText = getNextSibling(src).innerText || getNextSibling(src).innerHTML;
        var nodeValue = GetNodeValue(getNextSibling(src));
        alert("Text: " + nodeText + "," + "Value: " + nodeValue);
    }
    var parentTable = GetParentByTagName("table", src);
    var nxtSibling = parentTable.nextSibling;
    if(nxtSibling && nxtSibling.nodeType == 1) {
        if(nxtSibling.tagName.toLowerCase() == "div") {
            CheckUncheckChildren(parentTable.nextSibling, src.checked);
        }
    }
    CheckUncheckParents(src, src.checked);
  }
}
```

In my next post, I will write about how to retrieve the value of selected nodes at the server side.

I hope this helps!

