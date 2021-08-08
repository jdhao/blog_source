---
title: "Converting PDF Pages to Images with Poppler"
date: 2019-11-14 22:44:21+0800
tags: [PDF]
categories: [technique]
---

In this post, I want to share how to convert PDF to images using the pdftoppm
command line tool.

<!--more-->

# Install

## Install on Windows

`pdftoppm` is provided by the [poppler](https://github.com/freedesktop/poppler)
project. For Windows, in order to install the latest version of poppler, you
can install it via [conda](https://anaconda.org/conda-forge/poppler):

```
conda install -c conda-forge poppler
```

On Windows, the `pdftoppm` tool will be installed in
`ANACONDA_ROOT/Library/bin`. You should add this directory to the Windows PATH.

You need to install newer version of pdftoppm to use some of its features, for
example, [exporting to JPEG format](https://stackoverflow.com/q/18719256/6064933)[^1].
Note that the poppler provided by [this page](https://blog.alivate.com.au/poppler-windows/) is too old.

## Install on Ubuntu

```bash
apt-get update && apt-get install -y poppler-utils
```

This package installs the poppler command line utilities, such as `pdftoppm`,
which we are going to use.

# How to use

To convert a single page of PDF to image, you can use the following command:

```
pdftoppm -singlefile -f 4 -r 72 -jpeg -jpegopt quality=90 presentation.pdf test_poppler
```

The PDF file we want to convert to images is `presentation.pdf`. The generated
image name prefix is `test_poppler`.  The image extension is decided by the
exported image format. A little explanation of the options:

+ `-singlefile`: only convert one page of PDF. It is used together with the `-f` option to convert a single PDF page.
+ `-f`: index of the PDF page you want to convert. The page index starts at 1.
+ `-r`: image DPI in both x and y direction. If you want to set DPI in x and y direction separately, use `-rx` and `-ry` instead.
+ `-jpeg`: convert PDF page to JPEG format.
+ `-jpegopt`: option used when convert PDF pages to JPEG images. For options and their meanings, see [here](https://www.mankier.com/1/pdftoppm).

According to my test, the `pdftoppm` command works great and is quick to
produce the need images.

# Using pdf2image

If you want to use Python, there is also a
[pdf2image](https://github.com/Belval/pdf2image) which is a thin wrapper around
pdftoppm. Make sure you have installed pdftoppm and set its PATH correctly.

In the following script, I show an example on how to use the package.

```python
from pdf2image import convert_from_path

def main():
    pages = convert_from_path("presentation.pdf", first_page=2,
                              single_file=True)
    pages[0].save("test_pdf2image.jpg", quality=85)

if __name__ == "__main__":
    main()
```

The function `convert_from_path()` will convert the PDF to a list of [PIL Image
object](https://pillow.readthedocs.io/en/stable/reference/Image.html). You can
then manipulate the images with the powerful functionality provided by the
Pillow package.

There also a few important parameters to note:

+ `dpi`: this change the size and quality of th generated images. If you want to generate high quality images, use a large dpi, e.g., 300.
+ `thread_count`: Use multi-threading to accelerate image generation. The author [suggests no more than 4
threads](https://github.com/Belval/pdf2image#performance-tips), however, I found more threads lead to lightly faster speed. You may tweak it to fit your
need.

I have also written a more detailed script to directly generate images from PPT
file on the command. You can find the script [here](https://github.com/jdhao/pptx_to_image).


# References

+ [Install poppler on Windows](https://stackoverflow.com/q/18381713/6064933).
+ [Install poppler on Ubuntu](https://stackoverflow.com/q/32156047/6064933).
+ [Parsing pdfs using Python](https://mikethecanuck.wordpress.com/2016/12/29/parsing-pdfs-using-python/).
+ [pdftoppm man page](https://www.mankier.com/1/pdftoppm).

[^1]: Note that older version of pdftoppm only support PPM and PNG format. Newer versions support exporting to JPEG and TIFF format image. You should check whether exporting to JPEG is supported by using `pdftoppm --help` in the command line.
