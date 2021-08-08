---
title: "A Guide on Using Unicode Characters in Matplotlib"
date: 2017-05-13 21:36:26 +0800
tags: [Matplotlib, font, Python, Unicode, encoding]
categories: [Python]
---

A few days ago, I found that Matplotlib can not show some Unicode characters
(some Chinese characters) using its default settings. In the rendered output
image, Chinese characters are shown as blank boxes. After reading a lot of
posts on this issue, I am finally clear about how to use Unicode characters
properly in Matplotlib.[^1]

<!--more-->

The reason that Chinese characters are shown as boxes is that Matplotlib's
default font does not support Chinese. In order to use Chinese, we need to tell
Matplotlib to use a font which supports Chinese. Or, more directly, we can
provide the path of a Chinese font to Matplotlib.

This post assumes that `fc-list` is available on your system. If this is not
the case, you should first install [fontconfig](https://www.freedesktop.org/wiki/Software/fontconfig/).
On Linux, fontconfig is usually pre-installed, so no need to worry. On Windows,
you can install [MiKTeX](https://miktex.org/) or [TeX Live](https://www.tug.org/texlive/)
in order to use `fc-list`.

# Specify Chinese font by name

The first way to use Chinese is to give a valid font name to Matplotlib. Before
going on to the next step, make sure that there are Chinese fonts on your
system. If that is not the case, or if you want to try a new Chinese font, for
example, [Source Han Serif](https://source.typekit.com/source-han-serif/)
recently released by Google and Adobe[^2].

## Find valid Chinese fonts

Matplotlib provides the [FontManager](https://matplotlib.org/api/font_manager_api.html)
class to deal with fonts. This class has an attribute `ttflist` which provides
a list of font files indexed by Matplotlib. We can also get each font's name
easily from this list. The problem is that we do not know which of these
fonts support Chinese.

This is when [fc-list](https://linux.die.net/man/1/fc-list) comes to help. This
command can help us find Chinese fonts installed on the system. The command
`fc-list :lang=zh` will list all the available Chinese fonts. But it should be
noted that not all Chinese fonts are indexed by Matplotlib. Some of the
[ttc](https://en.wikipedia.org/wiki/TrueType#TrueType_Collection) font files [can not be used by Matplotlib](https://github.com/matplotlib/matplotlib/issues/8612).
So we need to find the intersection of available Chinese fonts and fonts indexed
by Matplotlib.

## Use a font

After we have found a valid Chinese font indexed by Matplotlib, we can tell
Matplotlib to use it by changing the [Matplotlib rc](https://goo.gl/M9E7i0) in
our script. In this way, all the following plotting command will use the new
font. Suppose that you have installed "STKaiti" on your system, the following
code will use it to render Chinese:

```python
import matplotlib as mpl
font_name = "STKaiti"
mpl.rcParams['font.family']=font_name
mpl.rcParams['axes.unicode_minus']=False # in case minus sign is shown as box

plt.text(0.5, 0.5, s=u'测试')
plt.show()
```

In the above code, `font_name` can be the name of any valid Chinese font. If
you just want to use a Chinese font for a particular command, you can directly
specify the font's name in the plotting command:

```python
plt.text(0.5, 0.5, s=u'测试', fontname=font_name)
```

This is my preferred way of using Chinese fonts with Matplotlib.

# Specify Chinese font by path

In order to use any font on our system, we should try the second way ---
directly give a font's path to Matplotlib. The code is like this:

```python
import matplotlib.font_manager as mfm
import matplotlib.pyplot as plt

font_path = "/usr/share/fonts/custom/simhei.ttf"
prop = mfm.FontProperties(fname=font_path)
plt.text(0.5, 0.5, s=u'测试', fontproperties=prop)
plt.show()
```

where `font_path` is the path to a Chinese font on your system.

# Conclusion

Both the two ways are easy to follow. You can choose whichever suits your need
best. For the complete code and also info on how to find available Chinese
fonts on your system, please refer to the code on [this repo](https://github.com/jdhao/matplotlib_chinese_font).

# References

+ [The first way](https://my.oschina.net/u/1180306/blog/279818)
+ [This page shows how to use the first method.](https://segmentfault.com/a/1190000005144275)
+ [This SO post also use the second way.](https://stackoverflow.com/q/7726852/6064933)
+ [Matplotlib official example on the second way.](https://matplotlib.org/examples/api/font_file.html)
+ [How to use the fontconfig pattern](https://linux.die.net/man/3/fcpatternformat)

[^1]: While this post is about how to show Chinese Characters, it should work for other languages too, should you change the settings accordingly.
[^2]: If you do not know how to install fonts on Linux or Windows, see [here](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/international_language_support_guide/add_fonts_all_users) and [here](https://www.fontspring.com/support/installing/how-do-i-install-fonts-on-my-windows-pc) for detailed guides.
