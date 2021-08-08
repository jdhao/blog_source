---
title: "How to Convert PDF to Images with Imagemagick"
date: 2019-11-20 22:20:41+0800
tags: []
categories: [Note]
---

Previously, I have talked about how to convert PDF to images using pdftoppm
[here](https://jdhao.github.io/2019/11/14/convert_pdf_to_images_pdftoppm/).
In this post, I want to share how to accomplish this task with
[Imagemagick](https://imagemagick.org/index.php).

<!--more-->

Imagemagick provides the `convert` tool that can be used to do various
complicated image processing tasks.

# Convert All Pages of PDF File to Images

Use `convert` to convert PDF pages to images with the following command:

```
convert -density 150 presentation.pdf -quality 90 output-%3d.jpg
```

In the above command, we convert all the pages of the PDF files to images.
`-density` is used to specify the DPI of the output images. For PDF files, it
must be the first option since PDF files has no notion of DPI. This option must
be used first so that `convert` know how to sample the PDF pages. `-quality`
specify the quality for the generated images.  `%3d` is used to specify the
format for generated image names. The generated images will be named
`output-001.jpg`, `output-002.jpg` ......

# Convert Single Page of PDF File to Image

To convert a single page of PDF to image, use the following command:

```
convert -density 150 presentation.pdf[0] -quality 90 test.jpg
```

The number inside the bracket is used to select a page. Note that the page
index starts at 0 instead of 1.

To resize the converted image, you can supply the `-resize` option:

```
convert -density 150 presentation.pdf[0] -quality 90 -resize 50% test.jpg
```

# Convert A Range of PDF Pages to Images

You can also specify a range of pages with the following command:

```
# convert from page 0 to page 5
convert -density 150 presentation.pdf[0-5] -quality 90 test.jpg
```

# References

+ https://aleksandarjakovljevic.com/convert-pdf-images-using-imagemagick/
+ https://askubuntu.com/questions/50170/how-to-convert-pdf-to-image
+ https://stackoverflow.com/questions/6605006/convert-pdf-to-image-with-high-resolution
+ [Convert multi-page PDFs to multiple images.](https://stackoverflow.com/a/24520738/6064933)
+ [Imagemagick: command line options.](https://imagemagick.org/script/command-line-options.php)
+ [Imagemagick: command line processing.](https://imagemagick.org/script/command-line-processing.php)
