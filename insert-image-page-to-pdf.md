---
title: How to Replace A Page of PDF File with Another PDF
date: 2018-06-04 14:18:34 +0800
tags: [PDF]
categories: [Note]
---

In this post, I want to write about how to replace one page of existing PDF
files with a new PDF image.

<!--more-->

# Crop image and convert it to PDF file

Because the page size of most PDF files is [A4
size](https://www.papersizes.org/a-paper-sizes.htm)[^1]. First we need to crop
the image to have the same aspect ratio as an A4 paper. We can use [FastStone
Image Viewer](http://www.faststone.org/FSViewerDownload.htm) to achieve this.

Open the image using FastStone Image Viewer, press `x` and we will be in the
crop mode. We need to add a new aspect ratio for the A4 size.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-6-4/94765087.jpg"
         title="Add a new aspect ratio"
         style="float: middle;">

In the pop-up window, click "Add" and set the width and height of the paper
ratios (see image below).

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-6-4/55857610.jpg"
         title="Set up new aspect ratio"
         style="float: middle;">

Then go back to the crop board and choose the ratio we have just set up. Then
choose the option "Lossless Crop to File". In the pop-up save file window,
choose the PDF format.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-6-4/83197851.jpg"
         title="Crop and save file as PDF format"
         style="float: middle;">

# Embed PDF image to the existing PDF files

Now we can embed the produced PDF image to the existing PDF files. We use the
free tool [pdftk](https://www.pdflabs.com/tools/pdftk-the-pdf-toolkit/) to do
this. Download, install it and set the `PATH` variable properly.

If we have a file `Thesis.pdf` and we want to replace the 5th page with another
file `sign_page.pdf`. We can use the following command:

```bash
pdftk A=Thesis.pdf B=sign_page.pdf cat A1-4 B1 A6-end output signed_thesis.pdf
```

This command is pretty self-explanatory. We concatenate page 1 to 4 of
`Thesis.pdf`, the `sign_page.pdf`and page 6 to end of `Thesis.pdf` to form a
new PDF file. In effect, we have replaced the 5th page of `Thesis.pdf`. The
produced file will be `signed_thesis.pdf`.

## Another way to embed PDF files.

If you have purchased Adobe Acrobat Pro, you can [use Acrobat to replace a page
in PDF
files](https://helpx.adobe.com/acrobat/how-to/replace-pages-in-pdf.html).

# Reference

+ <https://superuser.com/questions/611227/how-to-replace-a-single-page-in-a-pdf-using-another-pdf-in-linux>
+ <https://askubuntu.com/questions/221962/how-can-i-extract-a-page-range-a-part-of-a-pdf>

[^1]: The A4 size is 210x297 in mm.
