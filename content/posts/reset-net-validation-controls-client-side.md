---
title: 'Reset .net validation controls client side'
date: 2013-04-02T23:28:00.000+05:30
draft: false
url: /2013/04/reset-net-validation-controls-client.html
tags: 
- JavaScript
- Validation
- .Net Framework
- Asp.net
---

On many occasions we provide form reset functionality and people handle it client side or server side. In general we validate input from the user and show proper validation message. But sometimes it happens that user has entered wrong input and thus it will display validation messages. Now if you have handled form reset functionality client side, you must reset all validation message as well. You can do it as shown in below code:

  
**Javascript:**  

```
  function ResetForm() {
            $('input\[type=text\], textarea').val('');
            Page\_ClientValidateReset();
            return false;
        }

        function Page\_ClientValidateReset() {
            if (typeof (Page\_Validators) != "undefined") {
                for (var i = 0; i < Page\_Validators.length; i++) {
                    var validator = Page\_Validators\[i\];
                    validator.isvalid = true;
                    ValidatorUpdateDisplay(validator);
                }
            }
        }
```

  
**HTML:**  

```
 <asp:Button ID="btnReset" runat="server" Text="Reset" OnClientClick="return ResetForm();" />
```

  
Now let’s understand what JavaScript properties and functions are.  
  
Page\_Validators : This is an array containing all of the validators on the page.  
  
isvalid: This is a property on each client validator indicating whether it is currently valid.  
  

ValidatorUpdateDisplay: Inbuilt function which checks whether validation control’s display property is set to dynamic, if yes then it will set css style of that validator to none.

  

So basically it loops through all the validators, set isvalid property to true and set validators’s visibility to none. Here we're not using form reset funtction to clear form fields as Reset doesn't clear a form, it simply sets the form value to their default which may or may not be blank. 

  
I hope it helps!