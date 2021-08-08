---
title: "如何在新版 MX 播放器中加入对 AC-3 音频支持"
date: 2018-04-24 14:18:11 +0800
tags: []
categories: [Android]
---

最近在使用 1.9.19 版本的 [MX 播放器](http://mx.j2inter.com/)播放视频的时候，提示不支持 E-AC-3 （全称为 Enhanced Audio Codec 3，是[AC-3](https://en.wikipedia.org/wiki/Dolby_Digital) 的升级版）音频格式。稍微查了一下，原因是由于版权问题，新版的 MX 播放器默认不包含 AC-3 解码器，我们需要自己下载安装解码器。下面简单介绍如何加入对 AC-3 音频的支持。

<!--more-->

# 确定自己手机的平台

由于手机平台的不同，AC-3 的解码器也不同，所以首先需要明确自己的安卓手机是什么平台，例如 `tegra2`, `tegra3`, `x86`, `neon` 等。打开 MX 播放器，然后依次打开右上角的 `帮助 -> 关于` 菜单，可以看到自己的手机平台，例如我的手机是 *ARMv7 NEON* 。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-4-24/92583192.jpg">
</p>

# 下载相应的解码器

根据自己的平台，下载对应该平台的解码器，具体下载地址为 [XDA 论坛](https://forum.xda-developers.com/devdb/project/?id=19112#downloads)，选择合适自己平台的下载即可，如果实在不确定自己的平台，就下载 `aio` 版本的解码器。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-4-24/58276458.jpg">
</p>

# 安装解码器

下载完解码器以后，进入 `设置 -> 解码器` 界面，在该页面的最下面「常规」一栏，点击「自定义编解码器」，然后找到刚才下载的 zip 文件，选中。软件会提示：

>重启应用以重新载入编解码器

点击「确定」，然后重启 MX 播放器。

# 确认解码器安装成功

重启进入 MX 播放器，再次进入`关于`页面，在手机版本显示的地方会增加提示：

>自定义编解码器 1.9.18.2

说明编码器已经安装成功了，现在就可以使用 MX 播放器播放含有 AC-3 音频的视频文件了。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-4-24/46650415.jpg">
</p>


# 参考资料

+ <https://forum.xda-developers.com/apps/mx-player/mx-player-custom-codec-dts-support-t2156254>
+ <https://www.3nions.com/fix-ac3-audio-format-not-supported-in-mx-player/>
