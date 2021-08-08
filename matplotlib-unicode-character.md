---
title: How to Plot Unicode Characters with Matplotlib
date: 2018-04-08 22:33:46 +0800
tags: [Unicode, font, 字体, Matplotlib]
categories: [Python]
---

This is a post which follows up my previous post on [how to use Chinese
characters with
Matplotlib](https://jdhao.github.io/2017/05/13/guide-on-how-to-use-chinese-with-matplotlib/).

<!--more-->

# Introduction

If we use some Unicode characters when plotting with Matplotlib, for example,
character `✹` (Unicode code point is
[`U+2739`](https://codepoints.net/U+2739)), you will find that the character
may not show up in the rendered image. The reason is simple: the default font
used by Matplotlib does not support this Unicode character.

In order to plot this Unicode character, we need to do two things. Firstly, we
need to find a font which supports this character. Secondly, we need to tell
Matplotlib to choose this font for rendering the character.

# Find a valid font

Since this character may not belong to a certain language. It is trickier to
find which font supports it. According to [post
here](https://stackoverflow.com/questions/43834362/python-Unicode-rendering-how-to-know-if-a-Unicode-character-is-missing-from-the),
we can use the Python package
[fontTools](https://github.com/fonttools/fonttools) to check if a character
exists in a certain font.

First, we need to install fontTools. If you are using pip, use the following
command to install fontTools,

```
pip install fonttools
```

If you are using [conda](https://conda.io/docs/), use 

```
conda install -c conda-forge fonttools
```

to install this package.

Then, we can use the following script to show a list of fonts which contain
this Unicode character.

```
from fontTools.ttLib import TTFont
import matplotlib.font_manager as mfm

def char_in_font(Unicode_char, font):
    for cmap in font['cmap'].tables:
        if cmap.isUnicode():
            if ord(Unicode_char) in cmap.cmap:
                return True
    return False

uni_char =  u"✹"
# or uni_char = u"\u2739"

font_info = [(f.fname, f.name) for f in mfm.fontManager.ttflist]

for i, font in enumerate(font_info):
    if char_in_font(uni_char, TTFont(font[0], fontNumber=0)):
        print(font[0], font[1])
```

The above script will print a list of font path along with their corresponding
font names. All these fonts support the queried character. Sample output on my
system is shown below:

```
/home/jdhao/util/anaconda3/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf/DejaVuSans-Oblique.ttf DejaVu Sans
/home/jdhao/util/anaconda3/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf/DejaVuSansMono.ttf DejaVu Sans Mono
/home/jdhao/util/anaconda3/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf/DejaVuSans.ttf DejaVu Sans
/home/jdhao/util/anaconda3/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf/DejaVuSans-Bold.ttf DejaVu Sans
/home/jdhao/util/anaconda3/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf/DejaVuSansMono-Bold.ttf DejaVu Sans Mono
/home/jdhao/util/anaconda3/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf/DejaVuSans-BoldOblique.ttf DejaVu Sans
/usr/share/fonts/dejavu/DejaVuSansCondensed-BoldOblique.ttf DejaVu Sans
/usr/share/fonts/dejavu/DejaVuSansCondensed-Bold.ttf DejaVu Sans
/usr/share/fonts/dejavu/DejaVuSansCondensed.ttf DejaVu Sans
/usr/share/fonts/dejavu/DejaVuSansCondensed-Oblique.ttf DejaVu Sans
/usr/share/fonts/gnu-free/FreeSerif.ttf FreeSerif
/usr/share/fonts/opensymbol/opens___.ttf OpenSymbol
```

# Plot this character

In order to plot this character with Matplotlib, we need to use
`FontProperties` class in Matplotlib to find this font. Then we can use this
font in the plotting command:

```
import matplotlib.pyplot as plt
import matplotlib.font_manager as mfm

font_path = '/usr/share/fonts/gnu-free/FreeSerif.ttf'
prop = mfm.FontProperties(fname=font_path) # find this font

# use the font in plotting command
plt.text(0.5, 0.5, s=uni_char, fontproperties=prop, fontsize=20)
plt.show()
```

In the above code, variable `font_path` is the path of a font supporting the
Unicode character.

We can also use the font name to look up a font,

```
import matplotlib.pyplot as plt
import matplotlib.font_manager as mfm

font_path = '/usr/share/fonts/gnu-free/FreeSerif.ttf'
prop = mfm.FontProperties(family='OpenSymbol')
plt.text(0.5, 0.5, s=uni_char, fontproperties=prop, fontsize=20)
plt.show()
```

Output image is shown below

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-4-8/70337151.jpg"
         title="sample output image"
         style="float: middle;">

---

# References
+ [Check if a font contains a character](https://stackoverflow.com/questions/43834362/python-Unicode-rendering-how-to-know-if-a-Unicode-character-is-missing-from-the)
+ [Matplotlib FontProperties class](https://matplotlib.org/api/font_manager_api.html#matplotlib.font_manager.FontProperties)
+ [Find which characters a font support](https://stackoverflow.com/questions/4458696/finding-out-what-characters-a-font-supports/6671357#6671357)
