---
title: "Shadowsocks 配置 obfs"
date: 2020-04-25T00:44:29+08:00
tags: []
categories: [Note]
---

最近几个月在手机上使用 justmysocks，感觉速度很慢，经常断流，连接不上。通过搜索，发现原因是使用的线路问题。为了学习之用，我们可以通过配置 [obfs](https://github.com/shadowsocks/simple-obfs) 增强服务稳定性。

<!--more-->

justmysocks 提供了五条线路，这五条线路使用的运营商不同，导致各条线路速度差异很大，经过测试，发现第三条线路速度比较稳定版。第三条线路使用的是中国电信的 [CN2 GIA](https://zhuanlan.zhihu.com/p/37615352)线路(可以简单认为该线路访问国外网站速度更快)，支持 obfs，但是需要进行额外的设置。以下为针对不同系统开启 obfs 的方法。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200425010603.png" width="400">
</p>

# 安卓手机配置

除了 ss 客户端，还需要安装 [simple-obfuscation](https://m.apk.1pure.com/simple-obfuscation/com.github.shadowsocks.plugin.obfs_local) 这个工具。打开 ss 客户端，添加新的配置，输入服务器地址，端口，密码。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200425005236.jpeg" width="400">
</p>

然后移动到配置最下面的插件部分，点击`插件`，选择 simple obfuscation.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200425005338.jpeg" width="400">
</p>

然后点击下面的`配置`，在弹出的页面，`obfuscation wrapper` 选择 `tls`，确认更改即可。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200425005417.jpeg" width="400">
</p>

最后保存这个新添加的配置，然后使用这个配置打开 ss。

# Mac 设置

Mac 客户端可以使用 [shadowsocks-NG](https://github.com/shadowsocks/ShadowsocksX-NG), 它自带了 obfs 插件，不需要额外再安装插件。设置 obfs 比较简单，在服务器设置那里，增加新的服务器，首先把线路 3 的 ip 以及端口，密码，加密方式等信息填好，然后 `插件` 那里填写 `simple-obfs`，插件选项填写 `obfs=tls`，保存配置即可。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200425010735.png" width="400">
</p>

# Windows 设置

Windows 下开启 obfs，首先下载 [obfs-local](https://github.com/shadowsocks/simple-obfs/releases/download/v0.0.5/obfs-local.zip)，解压到和 shadowsock.exe 同一个目录。然后添加新的服务器，填入线路3或者4的信息都可以，`插件程序` 栏目填 `obfs-local`，`插件选项`栏目填 `obfs=tls`，保存配置，使用该服务器即可。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200504225114.png" width="400">
</p>

# 总结

经过测试，使用了 obfs 以后，访问谷歌的速度比之前快了一些，也更加稳定，延迟比较低，推荐使用。

# 参考

- [安卓设置 obfs](https://justmysocks.net/members/index.php?rp=/knowledgebase/7/How-to-use-Just-My-Socks-on-an-Android-phone.html).
- [Mac 设置 obfs](https://justmysocks2.net/members/index.php?rp=/knowledgebase/8/How-to-use-Just-My-Socks-on-a-Mac.html).
- https://ijustmysocks.com/122.html
