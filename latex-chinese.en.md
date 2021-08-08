---
title: How to Use Chinese with LaTeX
date: 2018-03-29 17:54:13 +0800
tags: [font, LaTeX]
categories: [LaTeX]
---

The original LaTeX do not have good support for Chinese. With the development
of LaTeX, we can now easily use Chinese in LaTeX document. In this post, I
would like to introduce several ways to work with Chinese in LaTeX.

<!--more-->

# Use CTeX

## Use ctexart documentclass ##

The [CTeX group](https://github.com/CTeX-org/ctex-kit) has developed multiple
document classes and packages to deal with the particular need of Chinese
typesetting.

If you have a long article mainly written in Chinese, you should choose
`ctexart` document class. An MWE is shown below:

```latex
\documentclass[UTF8]{ctexart}

\begin{document}
你好，这是一个测试文档。
\end{document}
```

When using the `ctexart` documentclass, you should also add
`\usepackage[T1]{fontenc}`. Otherwise, some of the characters [will be shown
incorrectly](https://tex.stackexchange.com/questions/664/why-should-i-use-usepackaget1fontenc).

## Use ctex package ##

Or you can use the `ctex` package instead:

```latex
\documentclass{article}
\usepackage[UTF8]{ctex}
\begin{document}
你好，这是一个测试文档。
\end{document}
```

You can compile it with `latex`, `pdflatex`, `xelatex` or `lualatex`. `xelatex`
is recommed from the author of this class.


# Use xeCJK with xelatex

If you only need to type a few Chinese character, you can go with `xeCJK`
package and compile the document with `xelatex`. A MWE is shown below:

<details>
<summary><font color="red">Click to check the code.</font></summary>

```latex
% this file can be compiled with xelatex command
\documentclass[12pt, a4paper]{article}
\usepackage{fontspec}
\usepackage[slantfont, boldfont]{xeCJK}

% set up English font
\setmainfont{Microsoft YaHei}
\setsansfont{Comic Sans MS}
\setmonofont{Courier New}

% set up Chinese font, the font must be valid on your system
\setCJKmainfont{Microsoft YaHei}
\setCJKmonofont{Source Code Pro}
\setCJKsansfont{YouYuan}

% correct line break for chinese
\XeTeXlinebreaklocale "zh"
\XeTeXlinebreakskip = 0pt plus 1pt

\title{测试}
\author{东}
\date{2016年6月6日}
\begin{document}
\maketitle
\begin{center}
满纸荒唐言\\
一把辛酸泪\\
都云作者痴\\
谁解其中味\\
\end{center}
\begin{verse}
\texttt{Stray birds of summer come to my window to sing and fly away}. \\
\textsf{And yellow leaves of autumn, which have no songs}, \\
\textrm{flutter and fall there with a sign}.\\
\hfill \emph{RabindranathTagore}
\end{verse}
\begin{verse}
\texttt{夏天的飞鸟}，\textsf{飞到我的窗前唱歌}，\textrm{又飞去了}。\\
秋天的黄叶，它们没有什么可唱，只叹息一声，飞落在那里。\\
\hfill \emph{罗宾德拉纳特·泰戈尔}
\end{verse}
\end{document}
```
</details>

For Chinese, the command `\setCJKmainfont{}` are used to set up the font used
by main text, which is also used by `\textrm{}` command. `\setCJKmonofont{}` is
used to set up the font used by `\texttt{}`. `\setCJKsansfont{}` is used to set
up the font used by `\textsf{}`.

So how to find a valid Chinese font to use in these command? If you have
installed [TeX Live](https://www.tug.org/texlive/), it is easy to do. Just use
the following command in your console:

```bash
fc-list :lang=zh
```

It will list all the fonts on your system which support Chinese. A sample
output is as follows:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-7/98274942.jpg" width="800"
title="List Chinese font names">
</p>

You can pick the font you like or experiment with different fonts to choose the
one you want.

The above example is based on a number of posts, but I have removed several
package since [they are now redundant](https://tex.stackexchange.com/q/2984/114857).

# References

+ [Solution from the developer of CTeX](https://tex.stackexchange.com/q/17611/114857).
+ <http://blog.sina.com.cn/s/blog_5e16f1770100lhbm.html>
+ <https://bit.ly/2H3Udob>
+ Some characters are wrongly displayed:
    + https://tex.stackexchange.com/q/2369/114857
    + http://texblog.net/latex-archive/uncategorized/symbols/
