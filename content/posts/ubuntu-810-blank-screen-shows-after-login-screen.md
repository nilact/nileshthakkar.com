---
title: 'Ubuntu 8.10, blank screen shows after login screen'
date: 2009-03-15T14:42:00.000+05:30
draft: false
url: /2009/03/ubuntu-810-blank-screen-shows-after.html
tags: 
- Linux
- Ubuntu
---

Recently i installed Ubuntu 8.10 (Intrepid Ibex) on my machine. After loging in i got only brown blank screen and i couldn't do anything except moving mouse around the screen. I thought i didn't installed it properly, so i reinstalled it twice. Finally i found out the solution. It happened because graphic card can not handle the compiz effects.  
  
There is a bug in the Intel Video driver for the Intel 830MG and 845G video cards. Desktop effects with Compiz will not work on those chips and freeze the system. During that time keyboard too won't work (meaning Numlock doens't work), just to be sure, are you able to get to consoles (ctrl+alt+f1)? If that too doesn't work, it's probably your kernel crashing.  
  
To fix the problem you need to remove the Compiz, here's how:  
  
1\. From the Grub boot menu slect the “recovery mode” option.

2\. Select “root Drop to root shell prompt”

Type the following at the prompt:

3\. sudo apt-get remove compiz

4\. sudo apt-get remove compiz-core

5\. exit

6\. Select “resume Resume normal boot”

Or you can use the following trick:

1\. Select FailSafe Terminal session from the options at Login screen and Login with your Username and Password.  

2\. Type gconf-editor. It will open configuration editor window, from it select  

desktop->gnome->application->window\_manager.

![configurationeditor](/images/b78a1-configurationeditor.png)

3\. From right side of the window edit the value for current and default by removing "usr/bin/compiz" with "usr/bin/metacity" and reboot.

4\. Select Gnome session and Login with your Username and Password.

That's it!! :)