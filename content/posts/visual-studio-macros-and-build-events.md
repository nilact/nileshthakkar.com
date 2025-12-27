---
title: 'Visual Studio Macros and Build Events'
date: 2013-04-14T17:07:00.000+05:30
draft: false
url: /2013/04/visual-studio-macros-and-build-events.html
tags: 
- Visual Studio
---

Visual studio has a nifty feature called Post/Pre Build events. These are used to include extra Dos commands and Macros and execute them before or after build.  
  
You can open it using **project properties** and it will open dialog as shown below:  
  
![BuildEvent Dialog](/images/53d3a-buildevent252520dialog_thumb25255b225255d.png)  
  
For post-build events you can define when to run commands included as shown in below snap:  
  
![Post-build events](/images/0b6d2-post-build252520events_thumb25255b325255d.png)  
  
You can click the respective Edit build buttons and it will open popup as shown below:  
  
![Build Popup](/images/9117d-build252520popup_thumb25255b125255d.png)  
  
By clicking the Macro button it will open macro listing and their respective value.  
  
By using Macros and dos command you can copy files from one location to another and run batch files and so on. In my current project I do have below command to copy xml file and email templates from common code project to my solution directory:  
  

```
RD "$(SolutionDir)WebAdmin\\XML"
MD "$(SolutionDir)WebAdmin\\XML"
COPY "$(ProjectDir)XML" "$(SolutionDir)WebAdmin\\XML"

RD "$(SolutionDir)WebAdmin\\EmailTemplates"
MD "$(SolutionDir)WebAdmin\\EmailTemplates"
COPY "$(ProjectDir)EmailTemplates" "$(SolutionDir)WebAdmin\\EmailTemplates"
```

  
In above code, $(SolutionDir) and $(ProjectDir) are macro names. Macros are non case-sensitive. Check out [this](http://msdn.microsoft.com/en-us/library/vstudio/42x5kfw4(v=vs.100).aspx) link that shows macros along with their descriptions.  
  
Use this nifty feature and automate your tasks ![Smile](/images/0d59a-wlemoticon-smile25255b225255d.png)