---
title: 'iTextSharp - Add Image from base64 string'
date: 2013-03-17T22:53:00.000+05:30
draft: false
url: /2013/03/itextsharp-add-image-from-base64-string.html
tags: 
- Asp.net
- Itextsharp
---

Itexhsharp is one of the popular third party component to create PDF with .Net framework.

If you're using Itextsharp for the very first time, I would suggest you to go through Mike's [series on Itextsharp](http://www.mikesdotnetting.com/Article/80/Create-PDFs-in-ASP.NET-getting-started-with-iTextSharp) that helps you to get started and [here](http://www.mikesdotnetting.com/Article/87/iTextSharp-Working-with-images) is the article from the series that shows different ways to add images to  Itextsharp.

To add image from base64 string, you first need to convert base64 string in to bytes array and then you can call GetInstance method of Itextsharp Image which accepts bytes array.

Let’s say we want to add below image to PDF:  

![Koala](/images/abf33-koala_thumb.jpg)  

If you’ve doubt whether base64string for any image is incomplete/corrupt, you can use [imagetobase64](http://webcodertools.com/imagetobase64converter/) converter and verify generated string. Check out below sample code to convert base64string to bytes array and add it in to PDF.

```csharp
private void CreatePDF()
    {
     //path you want to store PDF 
        string pdfPath = string.Format(@"E:\PDF\{0}.pdf", DateTime.Now.ToString("yyyy-MM-dd hhmmss"));

        using (FileStream msReport = new FileStream(pdfPath, FileMode.Create))
        {
            //step 1
            using (Document pdfDoc = new Document(PageSize.A4, 10f, 10f, 140f, 30f))
            {

                // step 2
                PdfWriter pdfWriter = PdfWriter.GetInstance(pdfDoc, msReport);

                //open the stream 
                pdfDoc.Open();

                iTextSharp.text.Image gif = null;

                string base64string = "yourbase 64 string";
                try
                {
                    //  Convert base64string to bytes array
                    Byte[] bytes = Convert.FromBase64String(base64string);
                    gif = iTextSharp.text.Image.GetInstance(bytes);

                }
                catch (DocumentException dex)
                {
                   //log exception here
                }
                catch (IOException ioex)
                {
                    //log exception here
                }
                gif.Alignment = iTextSharp.text.Image.ALIGN_LEFT;
                gif.Border = iTextSharp.text.Rectangle.NO_BORDER;
                gif.BorderColor = iTextSharp.text.BaseColor.WHITE;
                gif.ScaleToFit(170f, 100f);
              
                pdfDoc.Add(gif);

                pdfDoc.Close();

            }
        }
    }
```

If base64string is not valid, it may throw error and if you want to continue execution you want to assign gif object some value in Catch block. Sample code will create PDF in portrait mode but if you want PDF be in landscape mode, use Rotate method as shown below. This is sample code and you can alter it as per your need.  

```csharp
Document pdfDoc = new Document(PageSize.A4.Rotate(), 10f, 10f, 140f, 30f);
```

Generated PDF will have image as shown below:  

![clip_image001](/images/eb446-clip_image001_thumb.png)  
I hope it helps!