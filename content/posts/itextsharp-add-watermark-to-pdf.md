---
title: 'iTextSharp–Add watermark to PDF'
date: 2013-12-31T21:56:00.000+05:30
draft: false
url: /2013/12/itextsharpadd-watermark-to-pdf.html
tags: 
- Asp.net
- Itextsharp
---

In last post [iTextSharp–Add header/footer to PDF](http://nilthakkar.blogspot.com/2013/11/itextsharpadd-headerfooter-to-pdf.html) we saw how to create pdf with header/footer. To add watermark to pdf, you need to create a class that implement **IPdfPageEvent** as shown in below code:  

```
 public class PdfWriterEvents : IPdfPageEvent
    {
        string watermarkText = string.Empty;

        public PdfWriterEvents(string watermark)
        {
            watermarkText = watermark;
        }
        public void OnStartPage(PdfWriter writer, Document document)
        {
            float fontSize = 80;
            float xPosition = iTextSharp.text.PageSize.A4.Width / 2;
            float yPosition = (iTextSharp.text.PageSize.A4.Height - 140f) / 2;
            float angle = 45;
            try
            {
                PdfContentByte under = writer.DirectContentUnder;
                BaseFont baseFont = BaseFont.CreateFont(BaseFont.HELVETICA, BaseFont.WINANSI, BaseFont.EMBEDDED);
                under.BeginText();
                under.SetColorFill(BaseColor.LIGHT\_GRAY);
                under.SetFontAndSize(baseFont, fontSize);
                under.ShowTextAligned(PdfContentByte.ALIGN\_CENTER, watermarkText, xPosition, yPosition, angle);
                under.EndText();
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(ex.Message);
            }
        }
        public void OnEndPage(PdfWriter writer, Document document) { }
        public void OnParagraph(PdfWriter writer, Document document, float paragraphPosition) { }
        public void OnParagraphEnd(PdfWriter writer, Document document, float paragraphPosition) { }
        public void OnChapter(PdfWriter writer, Document document, float paragraphPosition, Paragraph title) { }
        public void OnChapterEnd(PdfWriter writer, Document document, float paragraphPosition) { }
        public void OnSection(PdfWriter writer, Document document, float paragraphPosition, int depth, Paragraph title) { }
        public void OnSectionEnd(PdfWriter writer, Document document, float paragraphPosition) { }
        public void OnGenericTag(PdfWriter writer, Document document, Rectangle rect, String text) { }
        public void OnOpenDocument(PdfWriter writer, Document document) { }
        public void OnCloseDocument(PdfWriter writer, Document document) { }
    }
```

Now let’s see how to add watermark using above class:  

```
PdfWriter pdfWriter = PdfWriter.GetInstance(pdfDoc, msReport);
pdfWriter.PageEvent = new PdfWriterEvents("Testing");
```

  
After using code above, it adds watermark as shown below:  
  
![clip_image001](/images/a2270-clip_image001_thumb25255b425255d.png)  
I hope it helps!