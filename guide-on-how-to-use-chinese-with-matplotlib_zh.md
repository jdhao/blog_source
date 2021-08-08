---
title: "解决 Matplotlib 使用中文乱码问题"
date: 2020-02-22 11:41:09+0800
tags: [Matplotlib, font, Python, Unicode, encoding]
categories: [Python]
---

注：这是一篇[旧文](https://jdhao.github.io/2017/05/13/guide-on-how-to-use-chinese-with-matplotlib/)的中文翻译版本。

前不久，我在使用 Matplotlib 画图的时候，发现一些 Unicode 字符(例如，汉字) 无法正常显示：在生成的图片中，汉字是乱码的，显示为一个方框。经过大量的查找和阅读，我终于明白了如何在使用 Matplotlib 时，正确渲染 Unicode 字符[^1]。

<!--more-->

简单来说，之所以中文字符被显示为方框，是因为 Matplotlib 默认使用的字体并不支持中文字符，并不是 Matplotlib 本身的原因。为了能够在图片上正确显示中文字符，我们需要指示 Matplotlib 使用一种支持中文的字体即可。或者，更直接地，我们在画图时可以直接给 Matplotlib 提供一个中文字体的路径。

本文假设 `fc-list` 命令安装在你的电脑上。如果你的电脑上没有这个命令，你应该先安装 [fontconfig](https://www.freedesktop.org/wiki/Software/fontconfig/)。在 Linux 系统上，这个程序通常是自带的，无需安装；在 Windows 系统上，可以安装 [MiKTeX](https://miktex.org/) 或 [TeX Live](https://www.tug.org/texlive/) 来使用 `fc-list` 命令。

以下介绍几种在 Matplotlib 中使用中文字符的不同方式。

# 指定中文字体名称

第一种使用中文的方式是给 Matplotlib 提供一个有效的中文字体名。再开始下面步骤之前，确保你的系统上已经安装了中文字体，如果你使用的是中文系统，这应该不是问题；或者如果你想使用一种新的中文字体，可以尝试 Google 和 Adobe 发布的 [Source Han Serif](https://source.typekit.com/source-han-serif/)。首先，你需要在系统上安装中文字体[^2]。

## 找到有效的中文字体名

Matplotlib 提供了[FontManager](https://matplotlib.org/api/font_manager_api.html) 类来处理字体相关的操作。这个类有一个 `ttflist` 属性，该属性提供了 Matplotlib 所能够发现到的字体列表。从这个字体列表，我们也可以很容易得到这些字体的名称。问题是，我们不清楚这些字体中有哪些字体是支持中文的。

这时候，我们就需要 [fc-list](https://linux.die.net/man/1/fc-list) 帮助了。这个命令行工具可以帮助我们找到系统上安装的中文字体：使用 `fc-list :lang=zh` 可以列出系统上可用的中文字体。值得注意的是，这些中文字体并非都可以被 Matplotlib 使用：Matplotlib 无法使用其中的 [ttc](https://en.wikipedia.org/wiki/TrueType#TrueType_Collection) 格式的字体(参见[这里](https://github.com/matplotlib/matplotlib/issues/8612))。所以我们需要得到 Matplotlib 索引的字体和系统提供的中文字体两个集合的交集。

## 使用字体

找到了 Matplotlib 索引的中文字体以后，我们可以通过更改 [Matplotlib rc](https://matplotlib.org/tutorials/introductory/customizing.html) 指示 Matplotlib 使用中文字体。这样设置以后，后续脚本中的画图语句都会使用新指定的中文字体。加入我们在系统中安装了 `STKaiti` 这个字体，下面的脚本将会使用这个字体来渲染图片中的中文：

```python
import matplotlib as mpl
font_name = "STKaiti"
mpl.rcParams['font.family']=font_name
mpl.rcParams['axes.unicode_minus']=False # in case minus sign is shown as box

plt.text(0.5, 0.5, s=u'测试')
plt.show()
```

在上面的代码中，`font_name` 可以任意一个有效的中文字体名。另外，如果你仅仅想在某个画图命令中使用中文字体，可以在画图命令中指定使用的字体名称，如下所示：

```python
plt.text(0.5, 0.5, s=u'测试', fontname=font_name)
```

这种方式是我认为比较好的在 Matplotlib 中使用中文的方式。

# 指定中文字体的具体路径

为了使用系统中的任何字体，我们也可以使用第二种方式：直接给 Matplotlib 提供一个字体的路径。代码如下：

```python
import matplotlib.font_manager as mfm
import matplotlib.pyplot as plt

font_path = "/usr/share/fonts/custom/simhei.ttf"
prop = mfm.FontProperties(fname=font_path)
plt.text(0.5, 0.5, s=u'测试', fontproperties=prop)
plt.show()
```

上述代码中，变量 `font_path` 是系统上某个中文字体的绝对路径。

# 结论

以上提供的两种方式都比较方便实用，我们可以根据自己的实际需要选取使用。要获取完整的代码，可以参考 [这里](https://github.com/jdhao/matplotlib_chinese_font)。

# 参考链接

+ 第一种方式
    + https://segmentfault.com/a/1190000005144275
    + https://my.oschina.net/u/1180306/blog/279818
+ 第二种方式
    + https://stackoverflow.com/q/7726852/6064933
    + https://matplotlib.org/examples/api/font_file.html
+ 两种方式都提到
    + https://blog.csdn.net/rumswell/article/details/6544377
    + https://blog.csdn.net/dgatiger/article/details/50414549
+ [如何使用 fontconfig 模式查找字体](https://linux.die.net/man/3/fcpatternformat)

[^1]: 尽管这篇文章是以中文为例，但是对于其他语言也是同样的道理，只需要相应设置即可。
[^2]: 如果不清楚如何在不同系统上安装新字体，可以查看 [这里](https://goo.gl/G4C42x) 和 [这里](https://goo.gl/3DRhFQ)的指导。
