---
title: "How to Resize, Pad Image to Square Shape and Keep Its Aspect Ratio in Python"
date: 2017-11-06 10:00:45 +0800
tags: [OpenCV, PIL]
categories: [Python]
---

When we are using convolutional neural networks, most of the time, we need to
fix the input image size to feed it to the network. The usual practice is to
resize the input image to the given size (the image aspect ratio is no longer
kept) and then crop a fixed size patch randomly from the resized image. This
practice may work well for image classification where fine details may not be
necessary. But for Image retrieval, we want to keep the image aspect ration
unchanged. In this post, I will summarize ways to resize an image to square
shape with padding and keep its aspect ratio.

<!--more-->

The main idea is to first resize the input image so that its maximum size
equals to the given size. Then we pad the resized image to make it square. A
number of packages in Python can easily achieves this.

# Using PIL

PIL is a popular image processing package in Python. We can use either `Image`
module or the `ImageOps` module to achieve what we want.

## Resize and pad with `Image` module

First we create a blank square image, then we paste the resized image on it to
form a new image. The code is:

```python
from PIL import Image, ImageOps

desired_size = 368
im_pth = "/home/jdhao/test.jpg"

im = Image.open(im_pth)
old_size = im.size  # old_size[0] is in (width, height) format

ratio = float(desired_size)/max(old_size)
new_size = tuple([int(x*ratio) for x in old_size])
# use thumbnail() or resize() method to resize the input image

# thumbnail is a in-place operation

# im.thumbnail(new_size, Image.ANTIALIAS)

im = im.resize(new_size, Image.ANTIALIAS)
# create a new image and paste the resized on it

new_im = Image.new("RGB", (desired_size, desired_size))
new_im.paste(im, ((desired_size-new_size[0])//2,
                    (desired_size-new_size[1])//2))

new_im.show()
```

## Resize and pad with `ImageOps` module

The PIL `ImageOps` module has a [`expand()
function`](https://pillow.readthedocs.io/en/stable/reference/ImageOps.html#PIL.ImageOps.expand)
that will add borders to the 4 side of an image. We need to calculate the
padding length in 4 side of the resized image before applying this method.

```python
delta_w = desired_size - new_size[0]
delta_h = desired_size - new_size[1]
padding = (delta_w//2, delta_h//2, delta_w-(delta_w//2), delta_h-(elta_h//2))
new_im = ImageOps.expand(im, padding)

new_im.show()
```

# Using OpenCV

In OpenCV, we have
[`copyMakeBorder`](https://docs.opencv.org/3.1.0/d3/df2/tutorial_py_basic_ops.html)
which is handy in making borders. The full code to resize and pad an image is
as follows:

```python
import cv2

desired_size = 368
im_pth = "/home/jdhao/test.jpg"

im = cv2.imread(im_pth)
old_size = im.shape[:2] # old_size is in (height, width) format

ratio = float(desired_size)/max(old_size)
new_size = tuple([int(x*ratio) for x in old_size])

# new_size should be in (width, height) format

im = cv2.resize(im, (new_size[1], new_size[0]))

delta_w = desired_size - new_size[1]
delta_h = desired_size - new_size[0]
top, bottom = delta_h//2, delta_h-(delta_h//2)
left, right = delta_w//2, delta_w-(delta_w//2)

color = [0, 0, 0]
new_im = cv2.copyMakeBorder(im, top, bottom, left, right, cv2.BORDER_CONSTANT,
    value=color)

cv2.imshow("image", new_im)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

I have upload the whole script to GitHub and you can download it
[here](https://gist.github.com/jdhao/f8422980355301ba30b6774f610484f2).

# References

+ [Stack Overflow post on how to add borders to an image.](https://stackoverflow.com/questions/11142851/adding-borders-to-an-image-using-python/39321668#39321668)
+ [OpenCV copyMakeBorder() method usage](https://docs.opencv.org/3.1.0/d3/df2/tutorial_py_basic_ops.html)
+ [Resize an image using PIL](https://stackoverflow.com/questions/273946/how-do-i-resize-an-image-using-pil-and-maintain-its-aspect-ratio)
