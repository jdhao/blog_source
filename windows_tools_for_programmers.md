---
title: "Windows 系统下几款程序员不可不用的神器"
date: 2019-01-07 10:08:00 +0800
tags: [Windows, Cmder, Listary, search, terminal]
categories: [tools]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/listary_search_file_apps.gif" width="800">
</p>

工欲善其事，必先利其器，好的工具能使人效率倍增，这篇博客介绍本人常用的一些提升效率的工具，希望能够帮到读者。

<!--more-->

# Listary (推荐指数★★★★★)

打开文件夹，文件和程序的时候，还在使用鼠标各种点点点？如果你经常需要打开程序或者打开某个文件夹，[Listary](https://www.listary.com/) 是你不可错过的工具，Listary 一款 Windows 系统下的程序启动、文件搜索工具，可以快速打开程序或者打开文件(夹)，同时 Listary 可以自动打开默认浏览器，并且使用指定的搜索引擎搜索用户输入的关键词，功能强大。最新版本的 Listary 6 beta，下载地址[见这里](https://www.listary.com/download/beta/listary6/ListaryInstaller.exe)。

安装以后，Listary 会在后台运行，双击 `Ctrl` 会打开 Listary 的运行界面（下载以后需要等待两三分钟，Listary 需要索引电脑上的文件，然后才能正常搜索文件和应用，刚下载就运行，可能搜索不到任何结果），直接输入文件或者目录的名称，或者输入应用的名称，可以快速搜索相关目录、文件和应用。

博文开头的图片展示了如何使用 Listary 搜索文件或应用，使用的时候，只需要输入文件夹或应用的关键词（如上图所示，`genverti`, `sublime`），Listary 即可智能把匹配度最高的结果放在第一位，然后使用 Enter 即可打开。

通常，在需要使用搜索引擎搜索某个内容的时候，我们会先打开浏览器，点击输入框，输入关键词，回车搜索，整个步骤非常繁琐。Listary 的另外一个强大功能是可以使用不同搜索引擎直接打开浏览器进行网络搜索，例如要使用谷歌搜索 `python list`，打开Listary，输入 `g`，然后按下空格，然后输入检索关键词 `python list`,回车，系统默认的浏览器将会打开谷歌搜索指定关键词的页面，非常方便。Listary 支持多个搜索引擎，例如百度（关键词 `b`），必应(关键词 `bing`)

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/listary_search_web.gif" width="600">
</p>

想要了解更多 Listary 用法，可以参考[这篇博文](https://jdhao.github.io/2017/03/01/Listary_intro/)。

# Cmder (推荐指数★★★★★)

还在使用没有自动补全功能，缺少 Linux 下各种常用命令，并且呆板生硬，缺少个性化配置的 Windows Cmd？是时候用一下 [Cmder](http://cmder.net/) 了， Cmder 是一款终端模拟器(terminal emulator)，类似 Windows CMD，但是功能比 CMD 强大太多，支持丰富的个性化主题。同时，Cmder 完整版包含了 [git for windows](https://jdhao.github.io/2017/03/01/Listary_intro/)，支持常用的 Linux命令，例如 `ls`, `cd`, `pwd`, `grep`, `find` 等，并且支持 Tab 自动补全。下载地址[点这里](https://github.com/cmderdev/cmder/releases/download/v1.3.11/cmder.7z)(免安装，解压即可使用).

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190107235642.png" width="800">
</p>

更多关于 Cmder 的使用，参考[这篇博客](https://jdhao.github.io/2017/12/29/cmder-usage/)。

# Vimium (推荐指数★★★★)

[Vimium](https://github.com/philc/vimium) 是一款 Chrome 浏览器插件，使你能够双手不离开键盘完成大多数网页操作。Vimium 作者对它的介绍是：

> The Hacker's Browser

Vimium 的快捷键设计模仿 Vim，对 Vim 熟悉的用户上手应该很容易。常用的快捷操作：

+ `gg`: 回到网页顶端
+ `G`: 回到网页低端
+ `j`: 往下滚动网页
+ `k`: 往上滚动网页
+ `d`：往下滚动半个网页
+ `u`: 往上滚动半个网页
+ `f`: 在当前标签页打开当前网页的某个链接，当前网页可用的链接会被高亮显示，并用字母表示，输入对应字母即可在当前标签页打开链接。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200426233820.jpg" width="800">
</p>

+ `F`: 和 `f` 功能类似，但是在新标签页打开链接。
+ `gi`: 激活当前网页可输入文件框，准备输入文字，例如打开 <https://github.com/>，按下 `gi`，此时光标会移动到左上角输入框，等待用户输入内容。
+ `b`: 在当前标签页打开收藏的某个网页，按下 `b`，会出现输入框，输入收藏网页关键词，选中一个条目，按下 Enter，在当前标签页打开收藏。
+ `B`: 类似 `b`，但是在新标签页打开收藏网页。
+ `o`: 在当前标签页，打开网页，收藏或者历史记录某个链接
+ `O`: 类似 `o`，但是在新标签页打开网页，收藏或历史记录某个链接
+ `yy`: 复制当前网页链接到剪切板
+ `p`: 在当前选项卡打开剪切板上的链接
+ `P`: 在新选项卡打开剪切板上的链接
+ `x`: 关闭某个选项卡
+ `X`: 重新打开上一个关闭的选项卡

更多操作，参考 Vimium [官方文档](https://github.com/philc/vimium#keyboard-bindings)。
