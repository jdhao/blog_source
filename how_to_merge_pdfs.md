---
title: "How to Merge PDF Files"
date: 2021-05-31T22:58:14+08:00
draft: false
tags: [PDF]
categories: [Note]
---

I have several PDF files that I want to merge to one file. Each page in the PDF
is actually an image. Here is what works best for me.

<!--more-->

# Use pdfunite from poppler-utils package:

Pdfunite provided by package [poppler-utils](https://en.wikipedia.org/wiki/Poppler_(software)#poppler-utils)
can be used to achieve our goal:

```
pdfunite a.pdf b.pdf output.pdf
```

The size of produced PDF file is small (about the sum of size of a.pdf and
b.pdf). The image quality is well preserved.

# Use convert from imagemagick:

```
convert a.pdf b.pdf output.pdf
```

The images in the produced pdf have very poor quality and is blurred to be
hardly recognizable. We can use `-desnity` option to increase the image quality:

```
convert -density 200 a.pdf b.pdf output.pdf
```

This time, the image quality improves greatly, but the size of produced file
also increases greatly (significantly larger that what is produce by `pdfunite`).

Overall, using pdfunite is the best choice.

# Refs

+ [Merge / convert multiple PDF files into one PDF](https://stackoverflow.com/q/2507766/6064933)
+ How to merge pdf:s using Imagemagick (resolution problem): https://superuser.com/q/54041/736190
