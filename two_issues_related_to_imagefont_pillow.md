---
title: "Two Issues Related to ImageFont Module in PIL"
date: 2018-12-04 22:48:00 +08:00
tags: [PIL, Windows, font, 字体, Unicode, Python]
categories: [Python]
---

In this post, I want to write about two issues related to the
[ImageFont](https://pillow.readthedocs.io/en/5.1.x/reference/ImageFont.html)
module in the [PIL](https://github.com/python-pillow/Pillow) package.

<!--more-->

# Can not use non-ASCII characters in font path on Windows

On Windows, if you use non-ASCII characters in font path in
[`ImageFont.truetype()`](https://pillow.readthedocs.io/en/5.1.x/reference/ImageFont.html#PIL.ImageFont.truetype)
method, you will get the following error:

```
OSError                                   Traceback (most recent call last)
<ipython-input-2-72cffff24bf9> in <module>()
----> 1 font = ImageFont.truetype("../测试.ttf")

D:\Anaconda\lib\site-packages\PIL\ImageFont.py in truetype(font, size, index, encoding, layout_engine)
    278
    279     try:
--> 280         return FreeTypeFont(font, size, index, encoding, layout_engine)
    281     except IOError:
    282         ttf_filename = os.path.basename(font)

D:\Anaconda\lib\site-packages\PIL\ImageFont.py in __init__(self, font, size, index, encoding, layout_engine)
    143         if isPath(font):
    144             self.font = core.getfont(font, size, index, encoding,
--> 145                                      layout_engine=layout_engine)
    146         else:
    147             self.font_bytes = font.read()

OSError: cannot open resource
```

This is an
[issue](https://github.com/python-pillow/Pillow/issues/3145#issuecomment-442469097)
related to how Unicode file paths are handled on Windows platform.

A simple solution is to rename the font file to include only ASCII characters.
Another solution is to read the font file in binary form. A working example is
shown below:

```python
from PIL import ImageFont
from io import BytesIO

with open("测试.ttf", "rb") as f:
    bytes_font = BytesIO(f.read())
font = ImageFont.truetype(bytes_font)
print(font.getsize("test"))
```

# Invalid reference when drawing text on image

When drawing text on image using PIL, you may see an error complaining about
invalid reference:

```
python draw_text.py
Traceback (most recent call last):
File "draw_text.py", line 12, in <module>
    drawer.text((10, 10), "大美中國", font=font, fill=(10, 10, 10))
File "D:\Anaconda\lib\site-packages\PIL\ImageDraw.py", line 275, in text
    *args, **kwargs)
File "D:\Anaconda\lib\site-packages\PIL\ImageFont.py", line 185, in getmask2
    size, offset = self.font.getsize(text, direction, features)
OSError: invalid reference
```

This is caused by a missing table in the font, which is discussed
[here](https://github.com/google/fonts/issues/132#issuecomment-244796023).  To
fix this problem, we need to use the open-source tool
[ttfautohint](https://www.freetype.org/ttfautohint/index.html).

After downloading this tool, we can process the font to get a fixed font:

```bash
ttfautohint test.ttf test_processed.ttf
```

After this process, you should be able to draw text on images using the
processed font without any error.
