---
title: 'Uploadify & FileHelper'
date: 2013-04-14T15:48:00.000+05:30
draft: true
url: 
---

using System; using System.Data; using System.Web; using System.Collections; using System.Web.Services; using System.Web.Services.Protocols; using System.IO; using FileHelpers; using System.Collections.Generic; namespace FUJIFILM.Con.Rep.WebAdmin { ///  
  
  
  
  
/// Summary description for $codebehindclassname$ /// \[WebService(Namespace = "http://tempuri.org/")\] \[WebServiceBinding(ConformsTo = WsiProfiles.BasicProfile1\_1)\] public class upload : IHttpHandler, FUJIFILM.Con.Rep.WebAdminPresenter.IView.IViewUsers { private FUJIFILM.Con.Rep.WebAdminPresenter.PresenterUsers \_PresenterUsers; private FUJIFILM.Con.Rep.Entity.Users \_CurrentUsers; private List \_listUsers; public void ProcessRequest(HttpContext context) { string savepath = ""; string tempPath = ""; context.Response.ContentType = "text/plain"; context.Response.Expires = -1; try { HttpPostedFile postedFile = context.Request.Files\["Filedata"\]; if (System.IO.Path.GetExtension(postedFile.FileName).ToLower() != ".csv") { context.Response.Write("Invalid File Type"); context.Response.StatusCode = 405; return; } tempPath = "uploads"; savepath = context.Server.MapPath(tempPath); string filename = postedFile.FileName; if (!Directory.Exists(savepath)) Directory.CreateDirectory(savepath); postedFile.SaveAs(savepath + @"\\" + filename); int totalImportedRecords = 0; List importedUsers = new List(); using (StreamReader sr = new StreamReader(postedFile.InputStream)) { FileHelperEngine engine = new FileHelperEngine(typeof(UserData)); foreach (UserData entry in engine.ReadStream(sr)) { entry.Password = Common.CommonFunctions.Encrypt(entry.UserName); importedUsers.Add(entry); } totalImportedRecords = engine.TotalRecords; } string strXML = Common.CommonFunctions.SerializeObject(importedUsers); \_PresenterUsers = new FUJIFILM.Con.Rep.WebAdminPresenter.PresenterUsers(this); \_PresenterUsers.ImportMultipleUsers(strXML); context.Response.Write(tempPath + "/" + filename); context.Response.StatusCode = 200; } catch (Exception ex) { FUJIFILM.Con.Rep.Common.ExceptionLog.WriteLog(ex); context.Response.StatusCode = 405; context.Response.Write("Error: " + ex.Message); } } public bool IsReusable { get { return false; } } #region IViewUsers Members public FUJIFILM.Con.Rep.Entity.Users CurrentUsers { get { return \_CurrentUsers; } set { \_CurrentUsers = value; } } public System.Collections.Generic.List listUsers { get { return \_listUsers; } set { \_listUsers = value; } } #endregion } \[DelimitedRecord(","), IgnoreFirst(1)\] public class UserData { public string ManagerName; public string Email; public string ContactNo; public string UserName; \[FieldOptional\] public string Password; public string ACManagerSAPId; } }  
  
  
  
```
 $(function() {
    
    $("#<%=FileUpload1.ClientID %>").uploadify({

        'uploader':'upload.ashx' ,
        
        'swf':'js/uploadify.swf',

        'cancelImg': 'images/uploadify-cancel.png',

        'buttonText': 'Browse',       

        'folder': 'uploads',

        'fileTypeDesc': 'CSV Files',

        'fileTypeExts': '\*.csv;',

        'multi': false,

        'auto': false,
        
         'onUploadError' : function(file, errorCode, errorMsg, errorString) {
            $('#<%= lblUploadMessage.ClientID %>').html('The file ' + file.name + ' could not be uploaded: ' + errorString);
        } 

    });
 function Upload()
    {
        $('#<%=FileUpload1.ClientID %>').uploadify('upload','\*');
        return false;
    }
 
  function resetForm()
    {
          $("input\[type=text\], textarea,input\[type='password'\]").val("");
         $("#spnmesg")\[0\].innerHTML = "";
         $("#<%= lblMessage.ClientID %>").html("");
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
 
 
 
   });


        

  </pre>
</div> 
```