---
title: "JPEG Image Orientation and Exif"
date: 2019-07-31 23:19:35+0800
tags: [Python, PIL, EXIF]
categories: [technique]
---

The other day I came across a strange bug during work. My colleagues gave me
some photographs taken with a smartphone. On their machines (Windows 7), all
the photos were shown correctly in landscape mode. However, when I checked
those photos, I found that some of those photos were shown in portrait mode
(rotated 90 or 270 degrees) or upside-down (rotated 180 degrees).

I was curious what happened and learned about
[Exif](https://en.wikipedia.org/wiki/Exif) and all its related stuff.

<!--more-->

# How Does Exif Orientation Work

## What is Exif

Exif (Exchangeable image file format) is a protocol to store various
meta-information about the images taken by digital cameras. Exif are stored
along with the actual image data. Some of the meta info in Exif includes camera
maker, shutter speed, focal length, orientation, shooting time etc. These meta
info is called tags and each tag has a specific tag number decided by the Exif
format standard. A comprehensive list of tags and their related informations
can be found [here](https://www.exiv2.org/tags.html).

## Exif Orientation Flag

Here, we are interested in the orientation meta info. When we are taking photos
with a camera, we may not always hold the camera in such a position that the
camera top corresponds to top of the scene. Image below from [this blog](https://www.impulseadventure.com/photo/exif-orientation.html)
illustrates this idea clearly:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190731231914.png">
</p>

But no matter how you are holding the camera, if you check the image on your
computer, the image is shown in correct orientation. This has something to do
with Exif orientation flag. When you are holding the camera with non-upright
position, the *raw photo* you take is stored as a rotated image. The digital
device (be a smartphone or digital camera) has a sensor to record the
orientation of the camera and writes that information into orientation flag in
Exif.

The Exif orientation flag can have nine different values from 1 to 9[^1]. The
image[^2] below shows eight of them:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190731232000.png">
</p>

Typically, you will only get flag 1, 8, 3, 6 for digital photos. Flag 2, 7, 4,
5 represent mirrored and rotated version of images.

## Why Are My Images Wrongly Displayed?

When you use a photo viewer to check the image on your computer, if the photo
viewer can read the Exif info and respects that info, it will automatically
rotate the raw image based on the orientation info. The end result is that you
can see the correctly-oriented photo no matter how it is actually stored.

Now come to the issue in the beginning of this post. According to
[this article](https://www.ivertech.com/Articles/Image-Rotation-Issue-With-Windows-10.aspx),
Windows systems before Windows 8 does not take into account the Exif
orientation flag and show the images as is, i.e., the raw, un-rotated images
are shown instead of the properly rotated images. Since the images my
colleagues gave me are shown correctly on their Windows 7 machine, we can draw
a conclusion that the raw images are in correct orientation. Somehow, the
smartphone is reporting the wrong orientation flags for some of the photos.
When I show these photos on my Windows 10 machine, since Windows 10 respects
the orientation flag, some of the images will be shown as rotated due to the
false orientation flag.

On the other hand, if you see correct photo on Windows 10 but rotated one on a
Windows 7 machine, that is because the raw image is in rotated position and
Windows 7 does not respect the orientation info in Exif.

# Read and Write Exif Info

## IrfanView

[IrfanView](https://www.irfanview.com/) is a great image viewer on Windows,
which respects the image Exif info. To view the image Exif info, open an image
and click `Image -> Information`. If the image contains Exif info, you can then
click the `EXIF info` button at the bottom left of the popup window to check
the image Exif info.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190731232045.png">
</p>

### Disable Auto-rotate for IrfanView

By default, IrfanView respects the Exif info and will auto-rotate the image
based on its orientation flag. To disable this behavior, go to `Options ->
Properties/Settings`, click `JPG/PCD/GIF` and uncheck the box `Auto-rotate
image according to EXIF info (if available)`.

## Pillow and Exif Info

If you read an image with [Pillow](https://github.com/python-pillow/Pillow) and
show it or save it again, Pillow will not respect the Exif orientation tag. You
may see such issues [here](https://github.com/python-pillow/Pillow/issues/183)
and [here](https://github.com/python-pillow/Pillow/issues/3748). There is a
[pull request](https://github.com/python-pillow/Pillow/pull/3687) to address
this issue, which has been merged now.

Pillow is able to read the image Exif info, but it is not able to edit the Exif
info. A sample script to show the image Exif info is shown below:

```python
from PIL import Image
from PIL. from PIL.ExifTags import TAGS

img = Image.open('test.jpg')

exif = img.getexif()

for k, v in exif.items():
    print('{}: {}'.format(TAGS[k], v))
```

In the above script, We use the
`Image.getexif()`[^3] to retrieve the image Exif info.
[`TAGS`](https://pillow.readthedocs.io/en/stable/reference/ExifTags.html)
is a dictionary mapping the tag number to a descriptive name.

## Piexif

The Python package [piexif](https://github.com/hMatoba/Piexif) can be used to
both read and write the image Exif info.

Based on the
[example](https://piexif.readthedocs.io/en/latest/sample.html#rotate-image-by-exif-orientation)
on its documentation site, I show an example of change the image Exif
orientation flag and save a new image using the new Exif info.

```python
from PIL import Image
import piexif

img = Image.open('test.jpg')
if "exif" in img.info:
    exif_dict = piexif.load(img.info['exif'])

if piexif.ImageIFD.Orientation in exif_dict['0th']:
    exif_dict['0th'][pixeif.ImageIFD.Orientation] = 3

    # quick and dirty work around to avoid type error
    exif_dict['Exif'][41729] = b'1'

    exif_bytes = piexif.dump(exif_dict)

img.save('new_img.jpg', exif=exif_bytes)
```

The primary info of Exif are stored in the `0th` key of `exif_dict`, which is
also a Python dictionary. It seems that piexif did not check the value type of
the Exif dict so that we may
[encounter ValueError](https://github.com/hMatoba/Piexif/issues/95)
when we try to dump the exif_dict. After reading the source code of piexif, I
use the following line of code to work around this issue just for now.

```python
exif_dict['Exif'][41729] = b'1'
```

After that, you should be able to dump the `exif_dict` without errors.

# References

+ [An online service which reads and writes Exif info](https://www.thexifer.net/#exif-camera-settings)
+ [Windows 10 auto-rotate images](https://answers.microsoft.com/en-us/windows/forum/windows_10-other_settings/disable-photo-auto-rotate-win-10/f4f56e93-ad1d-4969-87a4-a0a0b4f3a41b)
+ [Why images do not always appear correctly rotated](https://www.howtogeek.com/254830/why-your-photos-dont-always-appear-correctly-rotated/)
+ [Disable auto-rotate in IrfanView](https://irfanview-forum.de/showthread.php?t=8245)
+ [Derotate image with exiftool](https://leancrew.com/all-this/2009/04/derotating-jpegs-with-exiftool/)
+ [JPEG rotation and EXIF orientation](https://www.impulseadventure.com/photo/exif-orientation.html)
+ [How does smartphone decide orientation](https://www.scienceabc.com/innovation/smartphones-change-orientation-horizontal-landscape-gravity-sensor-accelerometer.html)
+ [JPEG Orientation](https://magnushoff.com/jpeg-orientation.html)
+ [Rotate image based on Exif orientation in Pillow](https://stackoverflow.com/questions/4228530/pil-thumbnail-is-rotating-my-image/6218425#6218425)
+ [Can not dump exif dict as bytes](https://github.com/hMatoba/Piexif/issues/83)
+ [Read image exif with Pillow](https://code.tutsplus.com/articles/why-are-my-mobile-phone-images-rotated-on-my-desktop--cms-30303)
+ [Exif orientation tag](http://jpegclub.org/exif_orientation.html)

[^1]: Flag 9 means *undefined*.
[^2]: Image source: https://www.daveperrett.com/articles/2012/07/28/exif-orientation-handling-is-a-ghetto/
[^3]: This method is added in [Pillow 6.0.0](https://pillow.readthedocs.io/en/stable/releasenotes/6.0.0.html#added-exif-class).
