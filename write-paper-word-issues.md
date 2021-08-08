---
title: "使用 Microsoft Word 撰写论文常见问题总结"
date: 2018-05-10 21:04:00 +0800
tags: [Office, Pandoc]
categories: [academic]
---

本文总结一些使用 Microsoft Word 2016 撰写论文时遇到的一些问题以及解决办法。

<!-- more -->

# Word 如何插入分节符？

「分节符」是 Word 中一个非常重要概念，很多设置都会涉及到分节符。分节符，顾名思义，就是把文档从某处分节（节的英语为 section）的标志。常用的分节符类型有「下一页」（即从当前位置的下一页开始新的一节），「连续」（即从当前位置开始新的一节）。具体如何插入分节符见下图（以插入「下一页」分节符为例）：

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-24/65597131.jpg" width="600">
</p>

# Word 中如何显示文中所有的控制符号，例如分页符，分节符等？

在 Word 中，默认情况下，分页符，分节符等控制符号，用户是看不到的，如果你不小心多插入了分节符，可能会造成意想不到的结果，但是很难找出原因，因此建议把显示控制符号的选项打开，帮助你了解文档中都有哪些字符。

如何打开这个选项？点击「开始」选项卡，然后找到「段落」部分，打开「显示/隐藏编辑标记」选项，如下图：

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-10/80421246.jpg">
</p>


或者使用 Word 提供的快捷键，<kbd>Ctrl</kbd>+<kbd>\*</kbd> 来开启或关闭这项功能。

# 如何在目录部分不显示目录本身的页码

你可能会遇到这种情况：在目录部分，目录本身作为第一项显示在上面（如下图所示）。

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-10/51081850.jpg"
         title="目录本身显示在了目录页面的第一项"
         style="float: middle;">

通常我们希望目录第一项能从摘要开始，不要包含目录自身。之所以出现目录自身，是因为目录也采用了一级标题的样式，因此生成目录的时候，Word 自动把目录本身也加入了进去。我们可以先清除目录的格式，然后再手动把目录设置为一级标题要求的字体以及字号，然后再更新目录，你会发现第一项从摘要开始了。

清除目录格式很简单，首先选中目录，在「开始」选项卡下，找到「样式」，然后点击下拉菜单，找到「清除格式」，点击即可（见下图）。

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-10/33224801.jpg"
         title="清除格式"
         style="float: middle;">

如何更新目录？点击「引用」选项卡，在目录部分，点击「更新目录」，然后选择「更新整个目录」即可，见下图：

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-13/17216292.jpg"
       title="如何更新目录"
       style="float: middle;">

# 如何在 Word 左边显示当前文档的目录导航？

在 Word 文档左边显示当前文档的结构，可以方便跳转到某一部分，作用类似 PDF 文件中的书签功能。如何开启？在 Word 中点击「视图」选项卡，然后找到「显示」相关的选项，选中「导航窗口」，目录就会出现在文档的左边（前提是你已经正确设置了各级标题），如下图所示：

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-10/65096163.jpg"
         title="在 Word 左边显示当前文档目录结构"
         style="float: middle;">

# 如何从摘要部分开始文档的页码？

摘要之前一般为目录，把光标置于目录页最后一页（假设目录有多页），然后插入一个类型为「下一页」分节符，你会看到目录页最后一页多了一个「分节符」的标志：

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-10/63054839.jpg"
         title="「下一页」类型的分节符"
         style="float: middle;">

然后如果此时双击该页的页脚，Word 会提示文档每一页位于哪一节，你会发现目录页与正文的摘要处于不同的节，这正是我们需要的效果，见下图，

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-10/58306924.jpg"
         title="分节符导致当前页与下一节位于不同的节"
         style="float: middle;">

然后把光标移到下一节页脚的位置，单击「设计」选项卡，找到「页眉与页脚」部分，点击「页码」下拉菜单，

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-10/73539599.jpg"
         title="页码菜单"
         style="float: middle;">

然后在菜单中选择「设置页码格式」，在弹出的窗口使用下面的设置，

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-10/93164935.jpg"
         title="设置页码格式"
         style="float: middle;">

如果目录页以及目录页之前的部分有页码出现，直接把光标移动到目录页页脚，删掉那些页码即可，这样论文就是从摘要开始编码了，起始页码为 1。

# 如何使得表格的宽度等于页面的宽度

在 Word 中，有的表格宽度很窄，如何使得表格的宽度等于当前 Word 页面的宽度呢？首先选中整个表格，然后单击「布局」选项卡，找到「单元格大小」栏目，点击「自动调整」下拉菜单，然后选择「根据窗口自动调整表格」选项，见下图

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-10/76474091.jpg"
         title="调整表格宽度为页面宽度"
         style="float: middle;">

# 如何插入公式

Word 中自带的插入公式功能简直是噩梦，效率极其低下。一种解决的办法就是安装第三方的公式辅助插件，例如 [MathType](http://www.dessci.com/en/products/mathtype/)（国外），或者 [AxMath](http://www.amyxun.com/)（国内），这两款软件都是收费的，但是后者价格更低，并且看起来做的更美观，可以考虑购买。另外一个非常不错的免费插件，叫做 texsword，可以直接在其中书写 LaTeX 公式，生成图片，插入到 Word 中，具体安装使用参见[这里](https://jdhao.github.io/2015/10/13/ms-office-latex-equation/)。

如果对 LaTeX 比较熟悉[^1]，还可以安装[Pandoc](https://github.com/jgm/pandoc/releases/tag/2.2.1)，

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-14/51230003.jpg"
       title="安装 Pandoc"
       style="float: middle;">

Pandoc 是一个非常强大的文件格式转换工具，我们可以利用它把 Markdown 文件格式转为 Word 的 docx 格式。首先新建一个后缀为 `md` 的文件（也就是 Markdown 格式的文件），在里面书写数学公式，然后利用 Pandoc 把该 md 文件转为 Word 的 docx 格式，

```
# 利用 Pandoc 把 md 文件转为 docx 文件
pandoc equation.md -t equation.docx
```

把生成的 `equation.docx` 文件中的公式拷贝到要使用公式的文档中即可。

# 如何删除页眉文字下面的横线

首先，双击页眉区域，进入页眉页脚编辑模式，然后选中页眉文字，点击「开始」选项卡，在段落部分，找到「边框」选项，然后选择「无框线」，这是页眉文字下面的横线就会消失。注意一定要选中页眉文字，见下方图片。

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-6-2/62444579.jpg"
         title="删除页眉横线"
         style="float: middle;">

# 参考

+ [如何在目录页不显示目录本身的页码](https://superuser.com/questions/262097/how-to-stop-table-of-contents-listing-itself-as-the-first-chapter)
+ [Word 显示控制字符](https://wordribbon.tips.net/T008879_Displaying_Nonprinting_Characters.html)
+ [去除页眉横线](https://www.datanumen.com/blogs/2-ways-remove-horizontal-lines-header-footer-word-document/)

[^1]: 不熟悉的话，可以看看 LaTex [输入公式的教程](https://www.sharelatex.com/learn/Mathematical_expressions)，还有这个 [LaTeX 中数学公式输入命令列表](https://reu.dimacs.rutgers.edu/Symbols.pdf)。
