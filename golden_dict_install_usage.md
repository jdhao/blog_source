---
title: "在 Listary 中调用 GoldenDict 或欧路词典查单词"
date: 2018-12-04 15:03:38+08:00
tags: [Listary, Windows]
categories: [tools]
---

[GoldenDict](https://github.com/goldendict/goldendict) 是一款开源字典应用，功能强大，支持多种字典格式，同时也支持网络资源。[欧路词典](https://www.eudic.net/v4/en/app/eudic) 是一款国产的查词软件，产品做得也很不错，同样支持多种字典格式。本文介绍 GoldenDict 以及欧路词典的配置, 以及如何在Listary 中调用它们实现查词功能。

<!--more-->

# GoldenDict 安装配置

GoldenDict 最新版可以从[这里](https://github.com/goldendict/goldendict/wiki/Early-Access-Builds-for-Windows)下载，下载直接解压即可，免安装。

## 安装词库

如前面所说，GoldenDict 支持多种字典格式，其中 MDX 文件是非常常用的字典格式，从[这里](https://freemdict.com/2018/06/05/%E7%89%9B%E6%B4%A5%E9%AB%98%E9%98%B6%E7%AC%AC%E5%85%AB%E7%89%88-%E7%AE%80%E4%BD%93/)可以下载到牛津高阶词典的 mdx 文件。下载以后解压。

打开 GoldenDict，进入 `编辑 -> 词典`，在默认的“词典来源”页面下，点击“文件”，然后点击“添加”，选择刚才解压牛津词典的文件夹，勾选 “递归搜索”，然后点击“重新扫描”，这样牛津词典就添加成功了。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181204223909.png" width="800">
</p>

点击 “词典”页面，可以看到牛津词典已经在词典列表里面了（默认的是维基百科词典）。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181204223953.png" width="800">
</p>


## 配合 Listary 使用

[Listary](https://jdhao.github.io/2017/03/01/Listary_intro/) 是一款非常好用的命令启动与文件搜索工具，能不能直接使用 Listary 来调用 GoldenDict 来查词呢？可以的，只需简单配置即可。

进入 Listary 的设置，然后点击 “关键字 -> 自定义”，点击左下角的 `+`，添加一个新的命令，配置如下：

```
关键字：gd
显示名称：search word with goldenDict
路径： <此处填写 goldenDict.exe 的完整路径，引号不是必须的>
参数： {query}
```

一个示例配置如下图所示：

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181204224032.png" width="400">
</p>

配置完成以后，使用 `Ctrl-Ctrl` 调出 Listary 的查询界面，然后输入 `gd <query>` 即可打开 GoldenDict 查询单词 `<query>`。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181204224151.png" width="400">
</p>

此时，GoldenDict 的简易查词界面将会弹出，给出所查单词的释义：

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181204224209.png" width="600">
</p>

# 欧路词典的配置

欧路词典下载地址为 <https://www.eudic.net/v4/en/app/download>,下载安装欧路词典以后，可以在设置里添加下载好的 mdx 词库，过程比较简单，这里不再赘述，免费版本默认支持两本外部词库。

与 GoldenDict 不同，要在 Listary 中调用欧路词典，需要指定 `--word` 或 `-w` 选项，使用最新的 Listary 6 版本，配置如下图所示：

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190111001652.png" width="600">
</p>


# 参考

+ <https://discussion.listary.com/t/listary-goldendict/3740>
+ <https://jdhao.github.io/2017/03/01/Listary_intro/#search-in-the-web-or-in-the-app>
+ [欧路词典如何安装词库](https://www.eudic.net/product/install.aspx)
