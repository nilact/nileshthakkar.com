---
title: 'iTextSharp–Print PDF'
date: 2013-09-30T22:36:00.000+05:30
draft: false
url: /2013/09/itextsharpprint-pdf.html
tags: 
- Asp.net
- Itextsharp
---

To print html page, we use  

```javascript
window.print();
```

but for PDF same can not be used as it won’t work. However iTextSharp provides a way to print PDF on client side with **AddJavaScript** method of PdfWriter.  

Check out below code:  

```csharp
private void CreatePDF()
    {
        string fileName = string.Empty;

        DateTime fileCreationDatetime = DateTime.Now;

        fileName = string.Format("{0}.pdf", fileCreationDatetime.ToString(@"yyyyMMdd") + "_" + fileCreationDatetime.ToString(@"HHmmss"));

        string pdfPath = @"E:\PDFs\" + fileName;

        using (FileStream msReport = new FileStream(pdfPath, FileMode.Create))
        {
            //step 1
            using (Document pdfDoc = new Document(PageSize.A4, 10f, 10f, 100f, 10f))
            {
                try
                {
                    // step 2
                    PdfWriter pdfWriter = PdfWriter.GetInstance(pdfDoc, msReport);

                    //open the stream 
                    pdfDoc.Open();

                    Phrase phrase1 = new Phrase("Hello world. Checking Print Functionality");

                    pdfDoc.Add(phrase1);

                    pdfWriter.AddJavaScript("this.print();");

                    pdfDoc.Close();

                }
                catch (Exception ex)
                {
                    //handle exception
                }

                finally
                {


                }

            }

        }
    }
```

It won’t print the PDF directly but will open print dialog as soon as you open the PDF. One can choose one of the listed printer and print it. There won’t be any print preview. On web page to simulate the print functionality, one can assign pdf path to Iframe which will be hidden on page.

```html
<iframe id="pdfViewer"  runat="server" style="display:none;" />
```

As you can see there’s no path assigned to src attribute in HTML but in code behind as shown below:  

```csharp
pdfViewer.Attributes["src"]=  ResolveClientUrl(string.Format("~/PDF/{0}",fileName));
```

It won’t display the PDF in browser but will ask user to print the PDF (without preview). Both Firefox and Chrome use in built pdf viewer.  

**Known Issues:**  

*   It will not display PDF in IE and Opera but download it if no pdf viewer is installed on machine and respective add-on is not installed on both the browsers. For opera 15 onwards, you can install [this add-on](https://addons.opera.com/en/extensions/details/pdf-viewer/?display=en) which is based on Mozilla’s pdf.js.
*   If pdf viewer has a setting to view pdf in secure mode and it is enabled, it will not run javascript and gives warning.

I hope it helps!