---
title: "Create Outline Text in Python with Pillow"
date: 2020-08-18 22:12:11+0800
tags: [PIL]
categories: [Python]
---

<details>
<summary><font size="2" color="red">update log</font></summary>

+ <font color="blue">2022-04-12: add warning about dangers of omitting stroke_fill parameter.</font>
</details>

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200818221311.png" width="400">
</p>

Sometimes, we may want to add outline color to text strokes with Pillow.
Prior to version 6.2.0, we can not do this in Pillow.
Fortunately, starting from version 6.2.0, pillow supports text outlines natively in its [`ImageDraw.text()`](https://pillow.readthedocs.io/en/stable/reference/ImageDraw.html#PIL.ImageDraw.ImageDraw.text) method.

<!--more-->

# The code

Here is a sample snippet to draw outline text:

```python
from PIL import Image, ImageFont, ImageDraw

def main():
    im = Image.new('RGB', (500, 500), (255, 255, 255))
    text = "测试文字"
    font = ImageFont.truetype(font="C:/WINDOWS/Fonts/STKAITI.TTF", size=70)
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

The parameter `stroke_width` controls the width of outline, and `stroke_fill` controls the outline color.

The above script will create an image like the title image.

Note that you must specify both `stroke_width` and `stroke_fill` when creating outline text.
If `stroke_fill` is omitted, it will default to text fill color, which will create terrible and illegible text on the image.

# References

+ [How can I draw text with different stroke and fill colors on images with python?](https://stackoverflow.com/q/8049764/6064933)
+ [Pillow 6.2 release note](https://github.com/python-pillow/Pillow/blob/ac4b7082c1990c8e1ab31da6945776a3cb1aefda/docs/releasenotes/6.2.0.rst)
