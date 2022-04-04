---
title: "How to Add Color Emoji to Image in Python"
date: 2022-04-03T11:52:55+08:00
markup: markdown
tags: [font, PIL, Unicode]
categories: [Python]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202204031210116.png" width="500">
</p>

In this post, I would like to share how to add [color emoji](https://en.wikipedia.org/wiki/Emoji) to image with Python.

<!--more-->

# Use Pillow

We can use Pillow directly to draw color emoji with color emoji fonts. Some of the available fonts are:

+ [NotoColorEmoji](https://github.com/googlefonts/noto-emoji): developed by Google and open source
+ [AppleColorEmoji](https://en.wikipedia.org/wiki/Apple_Color_Emoji): developed by Apple and used on Apple devices. Not free.
+ [Microsoft segoe ui color emoji](https://docs.microsoft.com/en-us/typography/font-list/segoe-ui-emoji): developed by Microsoft, not free.
+ [twemoji](https://github.com/twitter/twemoji): developed by Twitter. You can view the twemoji [here](https://twemoji-cheatsheet.vercel.app/)[^1].

[Symbola](https://aur.archlinux.org/packages/ttf-symbola) is also a emoji font, but it seems that it does not have color.

Here is a simple script to try those fonts:

```python
from PIL import Image, ImageDraw, ImageFont

back_ground_color = (50, 50, 50)

# unicode_text = "some test\U0001f602"
unicode_text = "\U0001f602"
im = Image.new("RGB", (500, 500), back_ground_color)
draw = ImageDraw.Draw(im)

# noto emoji, bit-map based, src: https://github.com/googlefonts/noto-emoji/blob/main/fonts/NotoColorEmoji.ttf
# apple emoji, bit-map based, src: https://github.com/samuelngs/apple-emoji-linux/releases
# segoe ui emoji, vector-based, works with different font size, font from here: https://fontsdata.com/132714/segoeuiemoji.htm (for test only)

font_info = {'symbola': [r"E:\symbola-font\Symbola-AjYx.ttf", 30],
             'notoemoji': [r"E:\NotoColorEmoji.ttf", 109],
             'appleemoji': [r"E:\AppleColorEmoji.ttf", 137],
             'segoeuiemoji': [r"E:\segoeuiemoji\seguiemj.ttf", 50]}

pos = [(100, 10), (50, 40), (50, 160), (100, 320)]

for i, item in enumerate(font_info.items()):
    path, size = item[1]
    font = ImageFont.truetype(path, size)
    draw.text(pos[i], unicode_text, font=font, embedded_color=True)

im.show()
```

Note that for bit-map font, you need to specify the exact font size embedded in it.
Examples are shown in this [PR](https://github.com/python-pillow/Pillow/pull/4955).
Otherwise, you will get [invalid pixel size error](https://github.com/python-pillow/Pillow/issues/6166).
For vector format color emoji font, you can use different font sizes just like using a normal font.

## special note on Apple Color Emoji

In the above script, I used [apple color emoji for linux](https://github.com/samuelngs/apple-emoji-linux), which is not the same as Apple Color Emoji provided on macOS.
Based on my experimentation, it only embed one resolution for emoji: 137.
However, the original Apple Color Emoji has [several resolutions](https://en.wikipedia.org/wiki/Apple_Color_Emoji#Implementation): 20, 32, 40, 48, 64, 96 and 160.

For example, the following works for me on macOS with font size 64:

```python
from PIL import Image, ImageDraw, ImageFont

back_ground_color = (50, 50, 50)

im = Image.new("RGB", (500, 200), back_ground_color)
draw = ImageDraw.Draw(im)

font = ImageFont.truetype('/System/Library/Fonts/Apple Color Emoji.ttc', 64)

unicode_text = "\U0001f602\u2764\U0001f639\U0001f335\U0001f525\U0001f992\u3297"
draw.text((10, 100), unicode_text, font=font, embedded_color=True)
im.show()
```

## Disadvantages

With this method, you can not mix text and emoji since these emoji font does not support normal text.
You need to write extra code to mix text and emoji naturally, which is no easy task.

# Use package Augly

[Augly](https://github.com/facebookresearch/AugLy) from Facebook also supports add emoji to image. It can randomly add an emoji to your image.

```python
import os

from PIL import Image

from augly.utils.base_paths import EMOJI_DIR
import augly.image as imaugs


def main():
    im_path = "./test.jpg"
    im = Image.open(im_path).convert('RGB')

    emoji_category = ['activity', 'alphanumeric', 'animals_and_nature', 'flags', 'food_and_drink',
                      'objects', 'people', 'smileys', 'symbols', 'travel_and_places']

    x, y = (0.8, 0.8)
    opacity = 0.7
    emoji_size = 0.2

    for c in emoji_category:
        emoji_dir = os.path.join(EMOJI_DIR, c)
        emoji_aug = imaugs.RandomEmojiOverlay(emoji_directory=emoji_dir, opacity=opacity,
                                              emoji_size=emoji_size, x_pos=x, y_pos=y)
        new_im = emoji_aug(im)
        new_im.save(f'cat-emoji-{c}.jpg')


if __name__ == "__main__":
    main()
```

Augly also has a [OverlayEmoji](https://augly.readthedocs.io/en/latest/augly.image.html#augly.image.OverlayEmoji) class to apply a specific emoji to image.
However, the emoji must be in PNG format, so it means you have to use png supplied by Augly, which is not flexible.


# Use package pilmoji

[Pilmoji](https://github.com/jay3332/pilmoji) supports multiple source of emoji and also supports mixing emoji and regular text.
Note that it requires Python 3.8 or higher.

```python
from pilmoji import Pilmoji
from PIL import Image, ImageFont

im = Image.new('RGB', (1024, 768))

drawer = Pilmoji(im)

FONT_PATH = './NotoSansSC-Regular.otf'
font_size = 30
font = ImageFont.truetype(FONT_PATH, size=font_size)

text = "Hello, world! 👋 Here are some emojis: 🎨 🌊 😎"
drawer((10, 10), text, font=font)

im.show()
```

Under the hood, it downloads the emoji in PNG format and [composite](https://jdhao.github.io/2022/04/01/image_alpha_composite_pillow/) them to the original image.

Pilmoji is easy to use and has done all the heavy lifting of mixing text and emoji for you.
It may be the best choice if we want to mix text and emoji and show them on image.

# References

+ https://en.wikipedia.org/wiki/Implementation_of_emojis
+ [Insert colorful emoji into an image(Python)](https://stackoverflow.com/q/64253461/6064933)

[^1]: There is twemoji-color-font [here](https://github.com/eosrei/twemoji-color-font), but it is in SVGinOT format, which [is not supported in Pillow](https://github.com/python-pillow/Pillow/issues/6170) yet.
