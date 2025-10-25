---
title: "Printing A5 size on A4 papers using iText"
date: "2012-12-02T17:28:42"
template: "post"
draft: false
slug: "/posts/printing-a5-size-on-a4-papers-using-itext"
category: "Programming"
tags:
  - java
  - itext
description: "Automating bulk printing A4-sized papers into A5 size"
---

My wife (edit in 2020: now ex-wife) asked me whether I can help her to print one of her ebook collections into A5 sized for better reading. 
We have a normal A4 printer at home, and I'm planning to print 2 A5 pages into one page of A4 paper. We also want to remove the complexities of sorting the pages manually after printing (total pages are slightly less than 1000 pages).

Cut to the chase, my rough idea is to:

* Divide the PDF pages into half. First 500 pages (let's called it head pages, for the sake of discussion) will be printed onto left hand side of the paper. The last 500 pages (also referred to as tail pages) will be printed onto right hand side of the paper.

* Since our printer does not support duplex printing, I need to print odds page first, then even pages. After doing some test prints, the position of the head pages switched with tail pages. Which means, for even pages, head pages on right hand side and tail pages on left hand side.

Everyone who knows OS X knows that [Preview](http://en.wikipedia.org/wiki/Preview_(Mac_OS)) is a really good app, in which functionality is quite close to Adobe Acrobat Professional with tighter integration to OS itself when modifying the PDF. Furthermore, [Automator](http://en.wikipedia.org/wiki/Automator_(software)) app is also bundle together to automate your workflow, including PDF manipulation. However, after spending 1/2 hour to determine whether these tools can help, I think I gave up on them. My need is more complex from what have been offered out-of-the-box.

So, I quickly remembered iText library. And, I wrote a Java routine to basically convert the document. I think, no point explaining everything what the code does. Well, you can ask me if you want to know more and I'll try to help out.

```java
package pdfmagic;

import java.io.FileOutputStream;
import java.io.IOException;

import com.lowagie.text.Document;
import com.lowagie.text.DocumentException;
import com.lowagie.text.Rectangle;
import com.lowagie.text.pdf.PdfContentByte;
import com.lowagie.text.pdf.PdfImportedPage;
import com.lowagie.text.pdf.PdfReader;
import com.lowagie.text.pdf.PdfWriter;

public class Interleave {

    public static void main(String[] args) throws IOException, DocumentException {
        PdfReader pdfReader = new PdfReader("/Users/mohdfaizhasim/Desktop/interleavepdf/all.pdf");
        PdfWriter pdfWriterOdds = null;
        PdfWriter pdfWriterEvens = null;
        Document documentOdds = null;
        Document documentEvens = null;
        try {
            int totalCombinedPages = pdfReader.getNumberOfPages() + (pdfReader.getNumberOfPages() % 2);
            System.out.println("totalCombinedPages = " + totalCombinedPages);
            int middlePage = totalCombinedPages / 2;
            Rectangle pageSize = pdfReader.getPageSize(1);
            float width = pageSize.getWidth();
            float height = pageSize.getHeight();
            float scaleFactor = width/height + 0.04f;
            documentOdds = new Document(new Rectangle(height, width));
            pdfWriterOdds = PdfWriter.getInstance(documentOdds, new FileOutputStream("combined-odds.pdf"));
            documentOdds.open();
            PdfContentByte pdfContentByteOdds = pdfWriterOdds.getDirectContent();
            for (int i = 0; i < middlePage; i++) {
                if (i % 2 == 0) {
                    documentOdds.newPage();
                    PdfImportedPage headPage = pdfWriterOdds.getImportedPage(pdfReader, i + 1);
                    pdfContentByteOdds.addTemplate(headPage, scaleFactor, 0, 0, scaleFactor, -19f, -17);

                    if (i != middlePage - 1 || (i == middlePage - 1 && pdfReader.getNumberOfPages() % 2 == 0)) {
                        PdfImportedPage tailPage = pdfWriterOdds.getImportedPage(pdfReader, middlePage + i + 1);
                        pdfContentByteOdds.addTemplate(tailPage, scaleFactor, 0, 0, scaleFactor, height / 2, -17);
                    }
                }
            }

            try {
                documentEvens = new Document(new Rectangle(height, width));
                pdfWriterEvens = PdfWriter.getInstance(documentEvens, new FileOutputStream("combined-evens.pdf"));
                documentEvens.open();
                PdfContentByte pdfContentByteEvens = pdfWriterEvens.getDirectContent();
                for (int i = 0; i < middlePage; i++) {
                    if (i % 2 == 1) {
                        documentEvens.newPage();
                        PdfImportedPage headPage = pdfWriterEvens.getImportedPage(pdfReader, i + 1);
                        pdfContentByteEvens.addTemplate(headPage, scaleFactor, 0, 0, scaleFactor, height / 2, -17);

                        if (i != middlePage - 1 || (i == middlePage - 1 && pdfReader.getNumberOfPages() % 2 == 0)) {
                            PdfImportedPage tailPage = pdfWriterEvens.getImportedPage(pdfReader, middlePage + i + 1);
                            pdfContentByteEvens.addTemplate(tailPage, scaleFactor, 0, 0, scaleFactor, -19f, -17);
                        }
                    }
                }
            } finally {
                if (documentEvens != null) {
                    documentEvens.close();
                }
                if (pdfWriterEvens != null) {
                    pdfWriterEvens.close();
                }
            }

            PdfReader pdfReaderEven = new PdfReader("combined-evens.pdf");
            try {
                documentEvens = new Document(new Rectangle(height, width));
                pdfWriterEvens = PdfWriter.getInstance(documentEvens, new FileOutputStream("combined-evens-reversed.pdf"));
                documentEvens.open();
                PdfContentByte pdfContentByteEvens = pdfWriterEvens.getDirectContent();
                for (int i = pdfReaderEven.getNumberOfPages() - 1; i >= 0; i--) {
                    documentEvens.newPage();
                    PdfImportedPage headPage = pdfWriterEvens.getImportedPage(pdfReaderEven, i + 1);
                    pdfContentByteEvens.addTemplate(headPage, 0, 0);
                }
            } finally {
                if (documentEvens != null) {
                    documentEvens.close();
                }
                if (pdfWriterEvens != null) {
                    pdfWriterEvens.close();
                }

                if (pdfReaderEven != null) {
                    pdfReaderEven.close();
                }
            }
        } finally {
            if (documentOdds != null) {
                documentOdds.close();
            }
            if (pdfWriterOdds != null) {
                pdfWriterOdds.close();
            }

            pdfReader.close();
        }

    }

}
```
 	
Note: I've applied some PDF position and size adjustments directly in the code to maximise the font size.
