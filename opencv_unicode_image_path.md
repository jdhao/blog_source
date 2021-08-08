---
title: "How to Read and Write Images with Unicode Paths in OpenCV"
date: 2019-09-11 00:23:41+0800
tags: [OpenCV, Unicode]
categories: [Python]
---

The other day, when I was using OpenCV to read some images and print the image
shape, I got an error:

> 'NoneType' object has no attribute 'shape'.

I was confused and do not know why since the image path is valid. It tooks me a
while to understand the cause.

<!--more-->

The reason is that OpenCV only accepts ASCII characters for image paths when
reading and writing images. There is an [open PR](https://github.com/opencv/opencv/pull/13368)
to add Unicode path support, but it has not been merged. However, there are
ways to work around this issue.

# Read images with unicode paths

First, we can read the image with the help of Numpy [`fromfile()`](https://docs.scipy.org/doc/numpy/reference/generated/numpy.fromfile.html)
method, and then use [cv2.imdecode()](https://docs.opencv.org/3.4/d4/da8/group__imgcodecs.html#ga5a0acefe5cbe0a81e904e452ec7ca733)
to read the image. Here is a sample code:

```python
import numpy as np
import cv2

# img with unicode path
im_path = "测试目录/test.jpg"
# img is in BGR format if the underlying image is a color image
img = cv2.imdecode(np.fromfile(im_path, dtype=np.uint8),
                   cv2.IMREAD_UNCHANGED)
```

In the above script, the image is read with numpy and converted to a
one-dimensional numpy ndarray of type `np.uint8`. Then we use `imdecode()` to
decode the ndarray to OpenCV image format. `cv2.IMREAD_UNCHANGED` is a flag for
`imdecode`[^1].

# Write images with unicode paths

To write images with unicode paths, we can first encode the image in OpenCV
format to one dimension numpy ndarray format. Then we convert this numpy
ndarray to image on disk with the [`tofile()`](https://docs.scipy.org/doc/numpy/reference/generated/numpy.ndarray.tofile.html)
method.

```python
import cv2
import numpy as np

img = cv2.imread("test.jpg", 1)
h, w = img.shape[:2]
im_resize = cv2.resize(img, (w//2, h//2))

im_save_path = "测试目录/small_img.jpg"
# encode the im_resize into the im_buf_arr, which is a one-dimensional ndarray
is_success, im_buf_arr = cv2.imencode(".jpg", im_resize)
im_buf_arr.tofile(im_save_path)
```

In the above code, `cv2.imencode()` will encode the image as a one-dimension
numpy array and `tofile()` will save this array into disk.

# References

+ [How to read image with unicode path in OpenCV](https://stackoverflow.com/q/43185605/6064933).
+ https://github.com/opencv/opencv/issues/4292
+ [Encode image using OpenCV](https://jdhao.github.io/2019/07/06/python_opencv_pil_image_to_bytes/#opencv).
+ [OpenCV: none type has attribute shape](https://stackoverflow.com/a/60332806/6064933).

[^1]: The complete list of flags available and their description can be found [here](https://docs.opencv.org/3.4.4/d4/da8/group__imgcodecs.html#ga61d9b0126a3e57d9277ac48327799c80).
