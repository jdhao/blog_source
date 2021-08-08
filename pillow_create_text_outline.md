---
title: "Create Outline Text in Python with Pillow"
date: 2020-08-18 22:12:11+0800
tags: [PIL]
categories: [Python]
---

Sometimes, we may want to add surrounding color to text strokes in Python.
Prior to version 6.2.0, we can not do this in Pillow. Fortunately, starting
from version 6.2.0, pillow now supports text outlines natively in the
[`ImageDraw.text()`](https://pillow.readthedocs.io/en/stable/reference/ImageDraw.html#PIL.ImageDraw.ImageDraw.text)
method.

<!--more-->

```python
from PIL import Image, ImageFont, ImageDraw

def main():
    im = Image.new('RGB', (500, 500), (255, 255, 255))
    text = "测试文字"
    font = ImageFont.truetype(
        font="C:/WINDOWS/Fonts/STKAITI.TTF",
        size=70)
    drawer = ImageDraw.Draw(im)

    fill_color = (255, 0, 0)
    stroke_color = (0, 0, 255)

    drawer.text((50, 10), text, font=font, fill=fill_color, stroke_width=1, stroke_fill=stroke_color)
    drawer.text((50, 80), text, font=font, fill=fill_color, stroke_width=3, stroke_fill=stroke_color)
    drawer.text((50, 150), text, font=font, fill=fill_color, stroke_width=5, stroke_fill=stroke_color)
    drawer.text((50, 220), text, font=font, fill=fill_color, stroke_width=7, stroke_fill=stroke_color)

    im.show("text-image")

if __name__ == "__main__":
    main()
```

The parameter `stroke_width` controls the width of outline, and `stroke_fill`
controls the outline color.

The above script will create the following image.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200818221311.png" width="400">
</p>


Ref:

+ [How can I draw text with different stroke and fill colors on images with python?](https://stackoverflow.com/q/8049764/6064933)
+ [Pillow 6.2 release note](https://github.com/python-pillow/Pillow/blob/ac4b7082c1990c8e1ab31da6945776a3cb1aefda/docs/releasenotes/6.2.0.rst)
