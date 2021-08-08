---
title: "Manipulating Images with Alpha Channels in Pillow"
date: 2019-03-07 16:21:57+0800
tags: [PIL, Windows]
categories: [Python]
---

In image processing, [alpha
channel](https://www.makeuseof.com/tag/alpha-channel-images-mean/) is usually
used to control the transparency of an image. In itself, the alpha channel is
just a gray level image[^1]. In this post, I want to talk about how to add an
alpha channel to RGB image and show it using the Pillow package.

<!--more-->

Suppose that we have the following image:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200222142548.jpg">
</p>

we can add an alpha channel to it using the [Image.putalpha()](https://pillow.readthedocs.io/en/stable/reference/Image.html#PIL.Image.Image.putalpha) method:

```python
from PIL import Image

im = Image.open("owl.jpg")
alpha = Image.new("L", im.size, 50)

im.putalpha(alpha)

im.show()
```

You might expect to see a new image with opaque color, right? But what shows up
is nothing different from the original image? Why? Do we use the `putalpha()`
method wrongly?

It turns out this has something to do with how image is shown using the
`show()` method on different platforms. From the documentation of
`Image.show()`:

```
Displays this image. This method is mainly intended for debugging purposes.

On Unix platforms, this method saves the image to a temporary PPM file, and calls either the xv utility or the display utility, depending on which one can be found.

On macOS, this method saves the image to a temporary BMP file, and opens it with the native Preview application.

On Windows, it saves the image to a temporary BMP file, and uses the standard BMP display utility to show it (usually Paint).
```

So on Windows, the new image is converted to
[BMP](https://en.wikipedia.org/wiki/BMP_file_format) format and shown using the
system default image viewer. During the process of format conversion, the alpha
transparency is apparently lost[^2]. That is why you see no difference between
and new image and original image.

To fix this issue, we can patch the Pillow code to use PNG format as default.
First, we need to find the root of Pillow package:

```
import PIL
print(PIL.__path__)
```

On my system, the output is:

> ['D:\\Anaconda\\lib\\site-packages\\PIL']

Go to this directory and open the file `ImageShow.py`.  I add the following
code after the line `register(WindowsViewer)`:

```python
    class WindowsPNGViewer(Viewer):
        format = "PNG"

        def get_command(self, file, **options):
            return ('start "Pillow" /WAIT "%s" '
                    '&& ping -n 2 127.0.0.1 >NUL '
                    '&& del /f "%s"' % (file, file))

    register(WindowsPNGViewer, -1)
```

After that, I can show the image with alpha channel correctly.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200222142659.png">
</p>

# References

+ [Image modes in PIL](https://pillow.readthedocs.io/en/5.1.x/handbook/concepts.html#concept-modes)
+ [Add alpha channel to image](https://stackoverflow.com/q/26534148/6064933)
+ [Convert RGBA image to RGB image](https://stackoverflow.com/questions/9166400/convert-rgba-png-to-rgb-with-pil)
+ [Image with alpha channel is not displayed correctly on Windows](https://github.com/python-pillow/Pillow/issues/3695)

[^1]: The pixel values are in the range (0, 255), where 0 will black out the image, 255 shows the original image and value between shows opaque image.
[^2]: The BMP file format seems to support alpha channel, according to [this wiki](https://en.wikipedia.org/wiki/BMP_file_format). But that is not guaranteed.
