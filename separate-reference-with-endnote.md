---
title: "使用 Endnote 为论文每个章节制作单独的参考文献列表"
date: 2018-03-24 15:18:00 +0800
tags: [bibliography, Office]
categories: [academic]
---

毕业季到了，如果使用 Word 来写毕业论文，论文写作中的参考文献处理也是一个非常重要的问题，在 Word 管理参考的比较好的方式是通过 [Endnote](http://www.ioa.cas.cn/xwzx/zhxw/201410/P020141013364219434960.pdf)。Endnote 是一个非常方便的可视化管理参考文献的工具，本文介绍如何使用 Endnote 结合 Microsoft Word 给每个章节分别设置参考文献列表。

<!--more-->

本文假设你对 Endnote 的使用有基本的了解，如果不了解，可以看[这里](https://www.jianshu.com/p/a35e6baa2d26)。要给每个章节设置参考文献，需要两个步骤：

+ 第一步，需要对你的输出 style[^1] 进行修改。
+ 第二步，在 Microsoft Word 中进行必要的设置。

# 修改 Endnote 的 style 设置

首先，我们需要对 Endnote 的 output style 进行修改，找到你目前正在使用的 style。以「IEEE Proceedings」style 为例来说明，我们需要编辑这个 style，参见下图

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-24/73488365.jpg"
         title="编辑 style">
</p>


在弹出的编辑界面，找到「Section」部分，选中「Create a bibliography for each section」。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-24/61270336.jpg"
         title="编辑 style 的 Seciton 部分">
</p>


**请特别注意：经过上面的设置，每个章节后的参考文献列表编号默认是重新从 1 开始编号。如果你想要每章后面的参考文献列表编号是连续的，那么请勾选「Create a bibliography for each section」下面的「Continue numbering...」。**

设置完以后，需要保存这个 style，Endnote 会提示你重新命名，例如，我们把刚才的「IEEE Proceedings」style 命名为「IEEE Proceedings bibliography」。

# Word 的设置

完成 Endnote 设置以后，需要对 Word 进行设置。现在，进入你正在编辑的 Word 文档，把光标移动到要插入参考文献列表的章节的末尾，选择 `布局 -> 分隔符 -> 分节符 ->下一页`（如果你的参考文献可以和论文的章节正文在一页，你可以选择`布局 -> 分隔符-> 分节符 -> 连续`），如下图所示

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-24/65597131.jpg"
         title="插入「下一页」分节符">
</p>

如果你要在多个章节后面分别插入参考文献列表，重复以上插入「分节符」的步骤，确保在要插入参考文献的章节后面都插入了「分节符」。

然后，点击 Endnote 在 Word 中的选项卡，在 Style 设置里选中我们刚才编辑保存的style，也就是 「IEEE Proceedings bibliography」，如下图所示：

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-24/62443014.jpg"
         title="设定输出 style">
</p>

点击「Update Citations and Bibliography」，更新参考文献列表，你会发现所有插入分节符的章节后分别出现了该章的参考文献列表。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-24/94521053.jpg"
         title="更新参考文献">
</p>

# 参考

+ <https://libguides.uthsc.edu/endnote/cwyw/separate-bibs>

[^1]: Endnote 中的 style 用来控制参考文献的在文章中以及文后的输出格式，不同的期刊，会议以及标准对格式的要求不同。
