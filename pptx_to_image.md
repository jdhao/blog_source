---
title: "Convert PPTX Slides to JPEG Images on Linux"
date: 2020-03-30 22:30:20+0800
tags: [Office, Ubuntu]
categories: [Linux]
---

A short post on how to convert PPTX file to images. There are two steps. The
first step is to convert PPTX to PDF, and the second step is convert PDF to
JPEG image.

<!--more-->

First, we need to install [libreoffice](https://www.libreoffice.org/):

```
apt update && apt install libreoffice
```

To install the latest version of libreoffice, run the following command instead:

```
# in order to use add-apt-repository command
apt install software-properties-common
apt-add-repository -y ppa:libreoffice/ppa
apt update && apt install libreoffice
```

# Step one: from PPT to PDF

To convert PPTX to image, we need to first convert it to PDF

## Use libreoffice directly

We can use `soffice` provided by libreoffice to convert pptx to pdf directly:

```bash
soffice --headless --convert-to pdf test.pptx
```

This will create a file named `test.pdf`.

## Use unoconv

Apart from soffice, we can also use [unoconv](https://github.com/unoconv/unoconv).
Install related package first:

```bash
apt update && apt install python3-uno unoconv
```

Then use unoconv to convert pptx to pdf:

```bash
unoconv -f pdf demo.pptx
```
I met the following error when I run unoconv:

```
unoconv: Cannot find a suitable pyuno library and python binary combination in /usr/lib64/libreoffice
ERROR: Please locate this library and send your feedback to:
[http://github.com/dagwieers/unoconv/issues](https://github.com/dagwieers/unoconv/issues)
No module named uno
unoconv: Cannot find a suitable office installation on your system.
ERROR: Please locate your office installation and send your feedback to:
[http://github.com/dagwieers/unoconv/issues](https://github.com/dagwieers/unoconv/issues)
```

For me the error is because unoconv is using the wrong python. In fact, unoconv
is just a python script with a [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)):

```
#!/usr/bin/env python3
```

Since I also installed python3 via Anaconda and add it to the system path, the
above shebang will actually use python3 from Anaconda, which is wrong.

The `uno.py` package is located in `/usr/lib/python3/dist-packages/uno.py`, and
we need to use the system python3.

So we need to change the shebang of unoconv to:

```
#!/usr/bin/python3
```

According to [comment here](https://github.com/unoconv/unoconv/issues/49#issuecomment-711037899), we can also sed to do this:

```bash
sed -i 's|#!/usr/bin/env python3|#!/usr/bin/python3|' /usr/bin/unoconv
```

# Step two: from PDF to image

In order to turn PDF to images, we can use imagemagick or poppler.

## With Imagemagick

We need to install imagemagick:

```bash
apt install imagemagick
```

Then we can convert PDF file to image using `convert`:

```bash
convert -density 150 test.pdf -quality 80 output-%3d.jpg
```

### Possible issues

During conversion, two errors occur after issuing the convert command:

```
convert-im6.q16: not authorized `multiple_img.pdf' @ error/constitute.c/ReadImage/412.
convert-im6.q16: no images defined `output-%3d.jpg' @ error/convert.c/ConvertImageCommand/3258.
```

For the first error, you can edit `/etc/ImageMagick-6/policy.xml` and change
the following line:

```
<policy domain="coder" rights="none" pattern="PDF" />
```

to

```
<policy domain="coder" rights="read|write" pattern="PDF" />
```

For the second error, this is because ghostscript has not been installed on the
system. Try to install it:

```bash
apt install ghostscript
```

After that, you should be fine to generate from PPTX to jpg/png
images.

## With poppler

We need to install poppler-utils:

```bash
apt-get update && apt-get install -y poppler-utils
```

For further steps, refer to [this post](https://jdhao.github.io/2019/11/14/convert_pdf_to_images_pdftoppm/).

<details>
<summary><font size="2" color="red">Changelog</font></summary>

+ <font color="blue">2020-12-24: Add how to convert pptx to pdf using unoconv.</font>
</details>

# References

+ [How to convert pptx files to jpg or png (for each slide) on linux?](https://stackoverflow.com/q/21523267/6064933)
+ [How can I convert a ppt to a pdf from the command line?](https://askubuntu.com/q/11130/768311)
+ https://jdhao.github.io/2019/11/20/convert_pdf_to_image_imagemagick/
+ [convert:not authorized `aaaa` @ error/constitute.c/ReadImage/453](https://stackoverflow.com/q/42928765/6064933)
+ [Can't convert PDF into image, because of 'no images defined' error](https://superuser.com/q/819277/736190)
+ [ImageMagick:convert-im6.q16: no images defined](https://askubuntu.com/questions/1181762/imagemagickconvert-im6-q16-no-images-defined)
