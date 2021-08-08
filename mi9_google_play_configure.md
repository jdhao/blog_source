---
title: "小米 9 安装谷歌商店（Google Play Store）与相关配置"
date: 2019-05-04 15:53:29+0800
tags: [MIUI, 小米]
categories: [Android]
---

五一假期入手了[小米9](https://zh.wikipedia.org/wiki/%E5%B0%8F%E7%B1%B39)，在安装谷歌 Play 商店以及启用过程中遇到了一些问题，特此记录解决方法。

<!--more-->

# 谷歌服务启用以及 Play store 安装

由于之前使用的手机是一加 3T，当时安装谷歌框架以及谷歌服务使用的是一个叫 [Go 谷歌安装器](https://www.coolapk.com/apk/com.goplaycn.googleinstall) 的应用，安装这个应用之后，它可以帮助我们自动安装所需要的所有谷歌套件。这次小米 9 到了以后，我也下载安装了这个应用，但是打开应用以后，它检测的结果是谷歌服务已经安装了，甚至谷歌 Play 商店也安装了，这让我感到奇怪。我在手机上找了一圈，的确找到了谷歌服务的开启开关，但是并没有找到谷歌 play 商店。

网上的各种教程满天飞，但是很多都是语焉不详，其实小米 9 的 MIUI 10.2.15.0 版本已经自带了谷歌服务，不需要安装谷歌服务了。谷歌服务具体打开方法为: 打开手机设置，搜素「谷歌」, 会看到「谷歌服务」, 点击进入，然后打开开关即可。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/resize_out.png">
</p>

接下来就需要安装谷歌 Play 商店了，可以从 [APKMIRROR](https://www.apkmirror.com/) 下载最新版的 Google play APK，点击 [这里](https://www.apkmirror.com/apk/google-inc/google-play-store/google-play-store-19-3-25-release/) 进入下载页面。下载以后安装即可。


## Play 商店卡在核对信息环节

装好 Play 商店，打开梯子，然后打开 Play 商店应用，一直卡在「正在核对信息」这个画面。

从网上找了各种帖子，有[一个帖子](https://www.xxshell.com/528.html)提到，可以从应用商店搜索「谷歌安装器」,然后到豌豆荚下载安装该应用，会自动安装所有需要的程序。经过测试，该应用在安装谷歌服务时，会出错，系统会提示「安装失败（-25）」，失败原因：「无法降级安装」，如下图所示：

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190504170612.jpeg" width="400">
</p>

反复尝试均无效，说明**该方法是有问题的，可能并不适用于最新的 MIUI 系统**。另外有人说是梯子不行，其实并不是，因为挂上梯子以后，在浏览器里面，是可以正常访问 Google 以及 YouTube 等网站的，说明并不是梯子本身的问题。

## 验证成功的方法

首先安装最新的谷歌 Play 服务，从 [这里](https://www.apkmirror.com/apk/google-inc/google-play-services/google-play-services-20-06-76-release/) 下载，下载的程序是可以顺利安装的。

接着确保系统中的谷歌服务是开启状态（参考上面的小米 9 谷歌服务启动方法）。

然后，安装 OurPlay 这个应用，从 [官网](https://www.ourplay.net/download) 可以下载，下载安装以后，点击进入底部的「启动」页面，OurPlay 会自动下载一些必要的组件。然后，在该页面的上部，选择「不加速」。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/Screenshot_20190504171752.jpg" width="400">
</p>

最后，重启手机，挂上梯子，打开 Play 商店，应该就不会卡在核对信息界面了。

## 未经验证的方法

也有人说并不需要这么麻烦，一直卡在核对信息界面，可能是因为没有清除 Play 商店的缓存等数据信息。

解决方法：进入系统设置，找到 `应用管理 -> 更多应用 -> Google Play 商店`，点击进入，然后点击「存储占用」, 进入新窗口，点击 `清除数据 -> 清除全部数据`。重启手机，挂好梯子，再登录 Play 商店，可以正常登录。

# 参考

+ [某乎上的方案](https://www.zhihu.com/question/48890950/answer/129175421)
+ 清除缓存和数据似乎可以参考 [这里](http://www.miui.com/thread-22507527-1-1.html) 以及 [这里](http://www.miui.com/thread-22479169-3-1.html).
+ [全部重装谷歌套件](http://www.miui.com/thread-18876132-1-1.html)，不清楚是否可行，可以一试.
+ [v2ex 上的类似话题](https://www.v2ex.com/t/548616).
