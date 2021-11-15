---
title: "Windows 下 LaTeX 手动安装宏包（package）以及生成帮助文档整套流程"
date: 2015-09-23 14:09:00 +0800
tags: [Windows]
categories: [LaTeX]
---

本文简单介绍如何手动安装一个 LaTeX 宏包。

<!-- more -->

一般来说，下载的 TeX 发行版已经自带了很多宏包，可以满足绝大部分需求，但是偶尔我们也可能碰到需要使用的宏包碰巧没有安装的情况，这时我们就需要自己下载安装宏包了。

下载 package 可以从 [CTAN](https://www.ctan.org/)（Comprehensive TeX Archive Network）下载。下载下来的宏包，有的直接包含 `.sty` 文件（`.sty` 文件就是 package 的最终形式）;有的下载下来只包含了 `.ins`, `.dtx`，以及一个 readme 文件，这时需要自己编译生成 `.sty` 文件，以及生成相应的 manual。下面详细介绍生成 `sty` 文件以及帮助文档的步骤。

# 生成 package 的步骤

以下步骤的前提是你已经安装好了 TeX 发行版，本文以 MikTeX 为例，请确保 MikTeX 的可执行程序已经加入了系统的环境变量中（PATH）。另外我的系统的 Win 8.1，win 7 应该也是一样的。

第一步：下载宏包，解压，不赘述，下载地址 <https://www.ctan.org/pkg>，我下载的一个宏包名称是 `qrcode`，是用来生成二维码的，以这个宏包的编译为例来讲解。

第二步：找到宏包解压后安装包地址，在我的电脑上是 `E:\LaTeX_package\qrcode`， 然后打开电脑的命令提示符界面，把当前的路径改到这个 package 所在的文件夹，也就是 `E:\LaTeX_package\qrcode`

第三步：生成 `qrcode.sty`，在命令提示符界面输入 `latex qrcode.ins`, 经过一段时间编译，`qrcode.sty` 就生成了；然后接着生成帮助文档 manual, 在命令提示符界面输入 `latex qrcode.dtx`,编译完成以后，会生成一个叫做 `qrcode.dvi` 的文件，这个文件也可以用 ctex 自带的 yap 程序打开，不过不是很方便，所以最好还是把它转为 PDF，需要两步操作，先输入 `dvips qrcode.dvi`，生成 `qrcode.ps` 文件，再输入 `ps2pdf qrcode.ps`，最终就生成了 `qrcode.pdf`的帮助文档，帮助文档包含这个 package 的基本用法，命令的格式等。最终完成的样子如下

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-22/32725143.jpg"
          title="生成的 package"
          style="float: middle;">

第四步：生成 `qrcode.sty` 文件以后还需要把这个文件放到 LaTeX 能够识别的路径下，在 ctex 下，以我的电脑为例，这个路径是 `D:\Program Files\CTEX\MiKTeX\tex\latex`,你可以在这个路径下建立一个文件夹，譬如 qrcode，然后把 `qrcode.sty` 放到这里。接着在你的电脑里找到 MikTeX 的 settings 这个程序，settings 有两个，选择后面括号里有 admin 的那个,打开以后，在 general 选项卡下有 Refresh FNDB 按钮，点击，过一会，这个 package 就会被加入 MikTeX 的路径中，然后在你的 tex 文件中就可以使用这个 package 了。

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-22/26501931.jpg"
         title="刷新 miktex 数据库"
         style="float: middle;">

# 使用

利用这个宏包，最后可以生成文本对应的二维码，代码如下：

```latex
\documentclass{article}
\usepackage{qrcode}
\begin{document}
This is a qrcode for Google HK.
\qrcode{https://www.google.com.hk/}
\end{document}
```

最后生成的二维码如下图：

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-22/23280720.jpg"
         title="利用 qrcode 库生成的 qrcode"
         style="float: middle;">

其他宏包如果要手动安装，都是类似操作的。Happy TeXing！

如果不想如此麻烦，可以选择使用 [TeX Live](https://www.tug.org/texlive/)，完整版已经预装了大多数你要使用的 package。

# 参考

+ <https://en.wikibooks.org/wiki/LaTeX/Installing_Extra_Packages>
+ <https://tex.stackexchange.com/questions/150182/dvi-to-pdf-convertor>


