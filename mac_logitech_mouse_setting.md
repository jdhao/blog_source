---
title: "Mac 上罗技 M590 鼠标设置"
date: 2019-04-02 01:50:31+0800
tags: []
categories: [Mac]
---

本文简单总结 Macbook Pro 上[罗技 M590 鼠标](https://item.jd.com/4462453.html)的设置。

<!--more-->

# 使用蓝牙连接电脑

罗技 M590 采用双模配置，可以使用蓝牙连接 Mac，也可以使用优联接收器连接 Mac。我选择使用蓝牙连接。 M590 鼠标滚轮下方有一个按钮，按钮后面有 1，2 两个数字配有指示灯，这个按钮用来切换鼠标连接电脑的方式。指示灯 1 亮的时候，表示将使用优联接收器与电脑连接，轻按切换按钮，会看到指示灯 2 开始缓慢闪动，表示我们将要使用蓝牙连接电脑。然后长按切换按钮 3 秒钟，指示灯开始快速闪动，此时鼠标进入了配对模式，在 Mac 的蓝牙设置搜索鼠标，然后点击连接即可。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200222144626.png" width="400">
</p>

# 配置

## Logitech Options

首先需要安装 [Logitech Options](https://support.logitech.com.cn/zh_cn/software/options) 软件，该软件用来配置鼠标各个按键的功能，安装完成以后打开，软件会自动识别当前和电脑连接的鼠标，点击鼠标的各个按键，可以对按键的功能进行设定，设定鼠标滚轮滚动速度，指针速度等。

### 罗技鼠标滚轮失灵的处理方法 ###

当你把罗技鼠标连接 Mac 以后，可能会发现鼠标的滚轮不起作用。可以进行如下设置：

1. 打开「系统偏好设置」，进入「安全性与隐私设置」
2. 在「隐私」页面的「辅助功能」选项，勾选「Logitech Options Daemon」

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200222144718.png" width="400">
</p>

然后，鼠标滚轮应该就可以使用了。

## 平滑滚动与反转鼠标滚动方向

如果你第一次使用 Mac 上鼠标，你会发现滚轮方向和 Windows 上是相反的，当鼠标滚轮向下滚动，网页或者文档页面是向上走的，反之，当鼠标滚轮向上滚动，网页或文档的页面是向下走的。并且，滚动不是特别平滑。为了改进这种行为，我们可以使用[Mos](https://github.com/Caldis/Mos)这款小工具调节鼠标的滚动行为。

安装 Mos 以后，在鼠标设置里面，把「平滑滚动」和「反转方向」选项打开，就可以获得比较舒适的鼠标体验了。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200222144808.png" width="400">
</p>

# 参考

+ <http://www.up4dev.com/2018/12/24/logintech-mouse-whele-not-working/>
+ https://www.v2ex.com/t/539054#;
+ <https://www.reddit.com/r/MacOS/comments/8ubvlh/mojave_beta_logitech_mx_master_scroll_does_not/>
+ <https://community.logitech.com/s/question/0D55A00007eQqkXSAS/scrolling-not-working-on-macos-mojave>
+ <https://support.logitech.com/en_us/article/Logitech-Options-permission-prompts-on-macOS-Mojave>
