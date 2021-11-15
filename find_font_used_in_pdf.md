---
title: "Find Which Font is Used in A PDF"
date: 2021-10-20T23:26:59+08:00
draft: false
tags: [PDF, font]
categories: [technique]
---

Sometimes, we are interested in which font is used for the main text in a PDF.
How do we find out?

<!--more-->

# Use Adobe Acrobat PDF reader

Open the PDF in [Adobe Acrobat](https://www.adobe.com/acrobat/pdf-reader.html)
and go to `File --> Properties`, under the `Fonts` tab page, a list of fonts
embedded in in the document are listed there.

However, there does not seem to be an option to view the font used by a
specific text in the document.

You can check the fonts whose encoding is `Custom` and look them up on Google
to see which one is what you want.

# Use pdffonts

We can also use `pdffonts` from the [poppler](https://en.wikipedia.org/wiki/Poppler_(software)) package. First, we need to install
poppler, I won't go into detail here, check [this post](https://jdhao.github.io/2019/11/14/convert_pdf_to_images_pdftoppm/) for the details.

After that, we can run pdffonts to find the interested fonts. It helps to
narrow to the page where the text occurs. For example, if we want to find font
for page 2, run the following command:

```
# -f: start page
# -l: end page
pdffonts -f 2 -l 2 test.pdf
```

This will greatly narrow down the range of fonts. You can then quickly find
which font the text uses.

# References

+ use pdfonts: https://superuser.com/a/628624/736190
+ Get PDF fonts info: https://stackoverflow.com/a/32426813/6064933
+ What is PDF: https://www.youtube.com/watch?v=GqEH8XvPZwM&t=619s
+ [Difference between full embedded fonts and embedded subset](https://publicatorcommunity.zmags.com/hc/en-us/articles/115002482043-Difference-between-full-embedded-fonts-and-embedded-subset)
