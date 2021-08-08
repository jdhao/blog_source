---
title: "Convert PIL or OpenCV Image to Bytes without Saving to Disk"
date: 2019-07-06 22:18:22+0800
tags: [PIL, OpenCV]
categories: [Python]
---

# Introduction #

Sometimes, we may want an in-memory jpg or png image that is represented as
binary data. But often, what we have got is image in OpenCV (Numpy ndarray) or
PIL Image format. In this post, I will share how to convert Numpy image or PIL
Image object to binary data without saving the underlying image to disk.

<!--more-->

If the image file is saved on disk, we can read it directly in binary format
with `open()` method by using the `b` flag:

```python
with open('test.jpg', 'rb') as f:
    byte_im = f.read()
```

Now the image will be read from disk to memory and is still in binary format.

What if we want to resize the original image and convert it to binary data,
without saving the resized image and re-read it from the hard disk? How should
we do it?

# Convert image to bytes

We can do it with the help of OpenCV or PIL.

## OpenCV

This is how to achieve that in OpenCV:

```python
import cv2

im = cv2.imread('test.jpg')
im_resize = cv2.resize(im, (500, 500))

is_success, im_buf_arr = cv2.imencode(".jpg", im_resize)
byte_im = im_buf_arr.tobytes()

# or using BytesIO
# io_buf = io.BytesIO(im_buf_arr)
# byte_im = io_buf.getvalue()
```

A little explanation here. [`imencode()`](https://docs.opencv.org/3.0-beta/modules/imgcodecs/doc/reading_and_writing_images.html#cv2.imencode)
will encode the Numpy ndarray in the specified format. This method will return
two values, the first is whether the operation is successful, and the second is
the encoded image in a one-dimension Numpy array.

Then you can convert the returned array to real bytes either with the
[`tobytes()`](https://docs.scipy.org/doc/numpy-1.15.0/reference/generated/numpy.ndarray.tobytes.html) method or
[`io.BytesIO()`](https://docs.python.org/3/library/io.html#binary-i-o). We can
finally get the `byte_im`. It is the same with saving the resized image in hard
disk and then reading it in binary format, but the saving step is removed and
all the operation is done in memory.

## PIL

If you like to use PIL for image processing. You can use the following code:

```python
import io
from PIL import Image

im = Image.open('test.jpg')
im_resize = im.resize((500, 500))
buf = io.BytesIO()
im_resize.save(buf, format='JPEG')
byte_im = buf.getvalue()
```

In the above code, we save the `im_resize` Image object into `BytesIO` object
`buf`. Note that in this case, you have to specify the saving [image format](https://pillow.readthedocs.io/en/5.1.x/handbook/image-file-formats.html#image-file-formats)
because PIL does not know the image format in this case. The bytes string can
be retrieved using `getvalue()` method of `buf` variable.

# References

+ [Python OpenCV convert image to byte string?](https://stackoverflow.com/q/17967320/6064933).
+ [Write OpenCV image in memory to BytesIO or Tempfile](https://stackoverflow.com/q/52865771/6064933).
+ [python Image PIL to binary Hex](https://stackoverflow.com/q/14921055/6064933).
+ [Convert PIL Image to byte array?](https://stackoverflow.com/q/33101935/6064933).
