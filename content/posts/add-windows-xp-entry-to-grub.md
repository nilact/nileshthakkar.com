---
title: 'Add Windows XP entry to GRUB'
date: 2009-01-22T22:13:00.000+05:30
draft: false
url: /2009/01/add-windows-xp-entry-to-grub.html
tags: 
- Linux
- Ubuntu
---

This time i am writing this post for those who are new to Linux environment just like me. Few Days back my one of the friend helped me to make my Windows XP default operating system when it boots. But it was not done properly so i decided to solve the problem. For Adding/ Changing boot option, one has to change the settings in the Menu.lst file in Linux (Mine is Ubuntu 7.10).  

  
For editing Menu.lst file you have to enter command as shown in the following image.  
  

![edit](/images/b9f68-edit.jpg)It will ask for administrator password if you are not having enough privileges. Once you are in that file then you will find explanation in that file, how to edit and all that stuff. Lines starting with # indicate that they are comments. Go through below image how to make entry of Windows XP in Menu.lst  

  
![Screenshot](/images/157c1-screenshot.png)  

If you are not able to see in the screenshot then you can go through below given lines. In your Menu.lst file, you will find option root(hd0,0) or similar to it. you have to set that option on the basis of your hardware configuration and on what partitions Operating Systems are installed.  
  

title Windows XP 64  
root (hd0,0)  
savedefault  
makeactive  
chainloader +1  
  

It would be better if you add above lines after all the entries of your Linux Operating System (Ubuntu) or you might not be able to set it as default O/S.  
  
If you are Windows User and find it somewhat difficult then you can download one GUI Tool "StartUp-Manager". StartUp-Manager, or SUM, is a python-glade GUI tool for configuring some things in grub and usplash.  
  
To download and install _StartUp-Manager_, download the DEB package attached to SUM’s official spot on the Ubuntu Forums [here](http://ubuntuforums.org/showthread.php?t=295524 "Ubuntu StartUp Manager"). **Homepage can be found [here](http://web.telia.com/%7Eu88005282/sum/index.html "StartUp Manager"). Here's the screenshot of it.  
  
  
**![mysnap](/images/71f51-mysnap.png)  

I hope this helps!! I am going to post solution of all those Dot Net questions very soon that are very common and often surfaces on the Asp.net Forums and Orkut Communities.  
  
Wish you all a great Weekend !!  

  
3/\\/J0! :)