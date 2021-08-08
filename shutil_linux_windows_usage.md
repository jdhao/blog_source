---
title: "How to Use shutil.move() on Windows and Linux"
date: 2018-11-15 20:58:00 +0800
tags: [Windows, CentOS, Python]
categories: [Note]
---

Today when I try to move a file using
[`shutil.move()`](https://docs.python.org/3/library/shutil.html#shutil.move) on
my Windows machine, I encounter an error message:

> PermissionError: [WinError 32] The process cannot access the file because it is being used by another process
>

In this post, I will write about what I have learned from this error.

<!--more-->

# How to move files correctly on Windows

On Windows, before moving a file, you must close it. Or, you will see the above
error message.

Suppose that we want to move images in a child directory `images/` to another
child directory `small_image/` if the width of an image is below a threshold.
On Windows system, the correct way to do it is like the following:

```python
from glob import glob
from PIL import Image

all_images = glob("images/*.jpg")

for i, im_path in enumerate(all_images):
    im = Image.open(im_path)
    width = im.width
    # we must close the image before moving it to another directory
    im.close()

    if width < 15:
        shutil.move(im_path, 'small_images/')

```

On Linux, you are not required to close the file before moving it, i.e., you
can move a file even if it is opened by another process.

# How to move a file if a file with the same name already exists in the destination directory?

On both Linux and Windows, when you try to move a file using `shutil.move(src,
dst)` with `dst` set as a `directory` path, you will encounter the following
error message if a file with the same name already exists under `dst`:

> shutil.Error: Destination path './test.txt' already exists
>

The solution is to use the full file path in `dst`, i.e., a complete path to
the new file. If a file with the same name exists under the destination folder,
it will be silently replaced. If that behaviour is not what you want, you may
consider renaming the file under the new directory.

# References
+ [Solve the issue of "file used by another process"](https://stackoverflow.com/questions/27215462/permissionerror-winerror-32-the-process-cannot-access-the-file-because-it-is/51126234#51126234)
+ [Solve the "file already exists" error when using `shutil.move()`](https://stackoverflow.com/questions/31813504/move-and-replace-if-same-file-name-already-existed-in-python).
+ [Different behaviour of shutil.move on Windows and Mac](https://stackoverflow.com/questions/26106645/different-behavior-of-shutil-move-on-windows-and-mac/53320468#53320468).
