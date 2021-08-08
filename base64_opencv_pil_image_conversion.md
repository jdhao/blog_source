---
title: "Conversion between base64 and OpenCV or PIL Image"
date: 2020-03-17 23:08:32+0800
tags: [PIL, OpenCV]
categories: [Python]
---

When we are building web services using Python, we often send or receive images
in [base64](https://docs.python.org/3/library/base64.html) encoded format.
However, when we are doing image processing tasks, we need to use PIL or
OpenCV. In this post, I will share how to convert between OpenCV or PIL image
and base64 encoded image.

<!--more-->

# base64 to PIL or OpenCV image #

## base64 to PIL Image

```python
import base64
from io import BytesIO
from PIL import Image

with open("test.jpg", "rb") as f:
    im_b64 = base64.b64encode(f.read())

im_bytes = base64.b64decode(im_b64)   # im_bytes is a binary image
im_file = BytesIO(im_bytes)  # convert image to file-like object
img = Image.open(im_file)   # img is now PIL Image object
```

In the above code, since `Image.open()` only accepts image path or file-like
object, we first convert the base64 encoded image to `BytesIO` object and then
read the image using PIL.

## base64 to OpenCV Image

```python
import base64
import numpy as np
import cv2

with open("test.jpg", "rb") as f:
    im_b64 = base64.b64encode(f.read())

im_bytes = base64.b64decode(im_b64)
im_arr = np.frombuffer(im_bytes, dtype=np.uint8)  # im_arr is one-dim Numpy array
img = cv2.imdecode(im_arr, flags=cv2.IMREAD_COLOR)
```

In the above code, we first convert binary image to Numpy array, then decode
the array with
[`cv2.imdecode()`](https://docs.opencv.org/3.4/d4/da8/group__imgcodecs.html#ga26a67788faa58ade337f8d28ba0eb19e).
The final `img` is an OpenCV image in Numpy ndarray format.

# PIL or OpenCV image to base64

## PIL Image to base64

```python
import base64
from io import BytesIO
from PIL import Image

img = Image.open('test.jpg')
im_file = BytesIO()
img.save(im_file, format="JPEG")
im_bytes = im_file.getvalue()  # im_bytes: image in binary format.
im_b64 = base64.b64encode(im_bytes)
```

In the above code, instead of saving the PIL Image object `img` to the disk, we
save it to `im_file` which is a file-like object. Note that in this case, we
need to specify the image format in `img.save()`.

## OpenCV to base64 image

```python
import base64
import numpy as np
import cv2

img = cv2.imread('test.jpg')
_, im_arr = cv2.imencode('.jpg', img)  # im_arr: image in Numpy one-dim array format.
im_bytes = im_arr.tobytes()
im_b64 = base64.b64encode(im_bytes)
```

In the above code, we first save the image in Numpy ndarray format to `im_arr`
which is a one-dim Numpy array. We then get the image in binary format by
using the [`tobytes()`](https://docs.scipy.org/doc/numpy/reference/generated/numpy.ndarray.tobytes.html)
method of this array.

# References

+ [Convert OpenCV or PIL image to bytes](https://jdhao.github.io/2019/07/06/python_opencv_pil_image_to_bytes/).
+ [base64 image to PIL Image](https://stackoverflow.com/a/26079673/6064933).
+ [Byte array to OpenCV image](https://stackoverflow.com/a/50444666/6064933).
+ [OpenCV image to base64](https://stackoverflow.com/q/40928205/6064933).
