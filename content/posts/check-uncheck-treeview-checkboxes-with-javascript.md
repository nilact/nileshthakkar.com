---
title: "Check Uncheck Treeview Checkboxes with Javascript"
description: "Client script to check or uncheck ASP.NET TreeView checkboxes and keep parents and children in sync."
summary: "Client script to check or uncheck ASP.NET TreeView checkboxes and keep parents and children in sync."
date: 2009-04-13T00:23:00.002+05:30
draft: false
url: /2009/04/check-uncheck-treeview-checkboxes-with_13.html
tags: 
- JavaScript
- TreeView
- Asp.net
---

As i said in my last post that i will post code how to retrieve treeview node values at client side as well as server side. But before that we have to add client script to check or uncheck treeview checkboxes.

First of all add following in your page_load event:

**C#**

```csharp
if(!IsPostBack)
{
    TreeView1.Attributes.Add("onclick","OnTreeClick(event)");
}
```

**VB**

```vb
If Not IsPostBack Then
    TreeView1.Attributes.Add("onclick","OnTreeClick(event)")
End If
```

**JavaScript**

```javascript
function OnTreeClick(evt) {
  var src = window.event != window.undefined ? window.event.srcElement : evt.target;
  var isChkBoxClick = (src.tagName.toLowerCase() == "input" && src.type == "checkbox");
  if(isChkBoxClick) {
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

function CheckUncheckChildren(childContainer, check) {
  var childChkBoxes = childContainer.getElementsByTagName("input");
  var childChkBoxCount = childChkBoxes.length;
  for(var i=0;i<childChkBoxCount;i++) {
      childChkBoxes[i].checked = check;
  }
}

function CheckUncheckParents(srcChild, check) {
  var parentDiv = GetParentByTagName("div", srcChild);
  var parentNodeTable = parentDiv.previousSibling;
  if(parentNodeTable) {
      var checkUncheckSwitch;
      if(check) {
          var isAllSiblingsChecked = AreAllSiblingsChecked(srcChild);
          if(isAllSiblingsChecked)
              checkUncheckSwitch = true;
          else
              return;
      }
      else {
          checkUncheckSwitch = false;
      }
      var inpElemsInParentTable = parentNodeTable.getElementsByTagName("input");
      if(inpElemsInParentTable.length > 0) {
          var parentNodeChkBox = inpElemsInParentTable[0];
          parentNodeChkBox.checked = checkUncheckSwitch;
          CheckUncheckParents(parentNodeChkBox, checkUncheckSwitch);
      }
  }
}

function AreAllSiblingsChecked(chkBox) {
  var parentDiv = GetParentByTagName("div", chkBox);
  var childCount = parentDiv.childNodes.length;
  for(var i=0;i<childCount;i++) {
      if(parentDiv.childNodes[i].nodeType == 1) {
          if(parentDiv.childNodes[i].tagName.toLowerCase() == "table") {
              var prevChkBox = parentDiv.childNodes[i].getElementsByTagName("input")[0];
              if(!prevChkBox.checked) {
                  return false;
              }
          }
      }
  }
  return true;
}

function GetParentByTagName(parentTagName, childElementObj) {
  var parent = childElementObj.parentNode;
  while(parent.tagName.toLowerCase() != parentTagName.toLowerCase()) {
      parent = parent.parentNode;
  }
  return parent;
}
```

Next time i will post about how to retrieve value at Client Side and Server Side.

I hope this helps!

