---
title: "全面总结如何在 LaTeX 中使用中文 (2020 最新版)"
date: 2018-03-29 17:54:13 +0800
tags: [font, 字体, LaTeX]
categories: [LaTeX]
---

LaTeX 最初对中文的支持并不友好，只支持 ASCII 字符，后来才逐渐变得好起来，加入了对 Unicode 字符的支持。本文总结一下几种在 LaTeX 中使用中文的方法，以期能够帮助读者快速使用 LaTeX 对中文内容进行排版。

<!--more-->

# 使用 CTeX

国内的 [CTeX 开发组](https://github.com/CTeX-org/ctex-kit)为了满足用户在 LaTeX 中排版中文的种种需求，已经开发了多款 LaTeX 文档类以及 LaTeX 宏包。使用这些库，你能够很轻松地在 LaTeX 使用中文。另外值得注意的是，CTeX 也提供所谓的 [CTeX 套装](http://www.ctex.org/CTeX)，其实是打包了 MikTeX 和其他的一些工具，现在已经基本上被淘汰了，非常老旧，**不建议**安装使用。建议读者直接安装 [MikTeX](https://miktex.org/) 或者 [TeX Live](https://www.tug.org/texlive/acquire-netinstall.html) 即可 (Mac 用户可以安装 [MacTeX](http://www.tug.org/mactex/))。

使用 CTeX 排版中文，目前主要可以使用两种方式：

+ 使用 ctexart 文档类（英文：documentclass）
+ 使用 ctex 宏包（英文：package）

## 使用 ctexart ##

如果你的文章主要由中文构成，你可以考虑使用 CTeX 中的 `ctexart` 文档类，下面的例子展示了如何使用 `ctexart` 文档类来写中文文档：

```latex
\documentclass[UTF8]{ctexart}
\usepackage[T1]{fontenc}

\begin{document}
你好，这是一个测试文档。
\end{document}
```

使用 `ctexart` documentclass 时候，最好加上 `\usepackage[T1]{fontenc}`，否则[某些符号显示不正确](https://tex.stackexchange.com/questions/664/why-should-i-use-usepackaget1fontenc)。

## 使用 ctex 宏包 ##

除了使用 `ctexart` 文档类，你可以可以选择使用 `ctex` 宏包，一个简单例子如下：

```latex
\documentclass{article}
\usepackage[UTF8]{ctex}
\begin{document}
你好，这是一个测试文档。
\end{document}
```

使用以上代码的 LaTeX 源文件可以使用 `latex`，`pdflatex`，`xelatex` 或者 `lualatex` 命令来编译生成 PDF 文件。CTeX 开发者推荐使用 `xelatex` 命令编译源文件。

# 使用 xeCJK

如果你仅仅需要在文档中使用有限的一些中文字符，你可以使用 `xeCJK` 宏包，然后使用 `xelatex` 命令编译源文件。一个简单可运行的例子如下：

<details>
<summary><font color="red">点击查看代码</font></summary>

```latex
% 该文件使用 xelatex 命令可以编译通过
\documentclass[12pt, a4paper]{article}
\usepackage{fontspec}
\usepackage[slantfont, boldfont]{xeCJK}

% 设置英文字体
\setmainfont{Microsoft YaHei}
\setsansfont{Comic Sans MS}
\setmonofont{Courier New}

% 设置中文字体
\setCJKmainfont{Microsoft YaHei}
\setCJKmonofont{Source Code Pro}
\setCJKsansfont{YouYuan}

% 中文断行设置
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

对于中文来说，`\setCJKmainfont{}` 命令用来设置正文使用的中文字体，同时也是 `\textrm{}` 命令使用的字体。`\setCJKmonofont{}` 用来设置 `\texttt{}` 命令中的中文使用的字体。`\setCJKsansfont{}` 用来设置 `\textsf{}` 命令中的中文使用的字体。

那么问题来了，如何找到可用的中文字体呢？如果你已经安装了 [TeX Live](https://www.tug.org/texlive/)，那么很容易找到中文字体。在系统的命令行，使用下面的命令：

```bash
fc-list :lang=zh
```

这个命令会列出系统中所有支持中文的字体，例如，在我的系统上部分输出如下：

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-7/98274942.jpg"
         title="列出中文字体名称"
         style="float: middle;">

你可选择自己喜欢的字体，或者可以实验不同字体的输出效果来选择合适的字体。

以上使用 xeCJK 的例子，综合参考了多个博文，但是我去掉了几个使用的宏包，例如`xunicode` 和 `xltxtra`，因为[这些宏包已经过时了](http://tex.stackexchange.com/questions/2984/frequently-loaded-packages-differences-between-pdflatex-and-xelatex)。

# 参考资料

+ [CTeX 开发者给出的解答，很全面。](https://tex.stackexchange.com/questions/17611/how-does-one-type-chinese-in-latex)
+ <http://blog.sina.com.cn/s/blog_5e16f1770100lhbm.html>
+ <https://bit.ly/2H3Udob>
+ 一些字符显示错误解决方案
    + https://tex.stackexchange.com/questions/2369/why-do-the-less-than-symbol-and-the-greater-than-symbol-appear-wrong-as
    + http://texblog.net/latex-archive/uncategorized/symbols/
