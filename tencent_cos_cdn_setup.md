---
title: "腾讯云对象存储博客图床开启 CDN 加速(不需要购买额外域名)"
date: 2020-03-16 00:48:59+0800
tags: [CDN, 图床, 对象存储]
categories: [Blog]
---
本文主要分享腾讯云对象存储开启 CDN 的方法，不需要购买额外的域名。

<!--more-->

之前博文记录过把博客的图床从七牛[迁移到了腾讯云对象存储](https://jdhao.github.io/2018/10/20/qiuniu_migrate_to_tencent_cos/)，刚开始用腾讯云，觉得花费不算很贵，一个月不超过 10 元。直到有一次发了[一篇关于机械键盘的文章](https://jdhao.github.io/2019/06/10/mechanical_keyboard_try/)，放了两张没有经过压缩的图片，由于访问人数稍微过大，几天时间就消耗了将近 40 元的流量费用。当时也在 V2ex 发了[帖子](https://www.v2ex.com/t/579567)，有人建议套个 CDN，因为 CDN 的流量相对于对象存储的流量更便宜。由于当时对 CDN 怎么使用不了解，以为还要自己再买一个域名，所以就没有深入了解下去。最近，趁着有时间，研究了一下博客的图床怎么使用 CDN，才发现原来设置很简单，也不需要购买域名。

# CDN 基本概念

设置 CDN 之前，需要理解几个相关的专业名词。

源站：简单理解，源站可以理解为最初的站点，由于这个站点的资源（图像，html，视频等）访问不方便，所以我们可以借助 CDN 来加速访问，减轻或者杜绝对源站资源的直接访问。相当 CDN 的节点服务器把源站的资料拷贝到自己的硬盘上，当有用户请求某个数据的时候，实际上访问到的是 CDN 的节点拷贝的数据。

回源：当源站的资料有更新，或者访问 CDN 节点缓存中不存在的某个资源，CDN 的节点会向源站请求更新的数据或者新的资料，这个过程就叫回源。CDN 可以设置回源时间，如果源站内容更新频繁，回源频率可以高一些，保证资料和源站一致，否则，回源的频率可以降低，减少对源站的压力和流量消耗。

回源鉴权：当 CDN 节点把源站的资料拷贝到该节点时，源站需要鉴定这些节点是否有从自己这里拷贝资料的适当权限，如果有权限，才可以拷贝资料。

# 腾讯云存储如何开启 CDN

首先，找到自己存放图片的云存储的存储桶，进入以后点击「域名管理」，会出现域名管理的页面。由于我不想自己申请域名，所以选择设置「默认加速域名」一项。「默认加速域名」意思就是使用腾讯云的 CDN 提供的默认域名访问自己的存储桶里面的图片，这样用户就不需要额外申请一个域名了。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/tencent_cos_default_acceleration_domain_name.jpg" width="600">
</p>

如果是第一次使用腾讯云的 CDN 服务，需要到 [CDN 的控制台](https://console.cloud.tencent.com/cdn)进行注册，注册以后，就会得到加速域名，下面的「源站域名」就是没有使用 CDN 的时候，访问我们建立的图床存储桶时使用的域名。这两个域名比较类似，只是把中间的代表存储桶区域的字符换成了 file，其余部分完全相同。在写博客引用图片的时候，应该使用新的加速域名来访问图片。如果一个图片之前的完整 URL 是：

```
https://blog-resource-1257868508.cos.ap-hongkong.myqcloud.com/test.jpg
```

那么该图片新的 URL 变成了：

```
https://blog-resource-1257868508.file.myqcloud.com/test.jpg
```

「加速地域」现在还不能选择，只能是「国内加速」，国内加速的含义是通过 CDN 节点来访问图片时，这些节点使用的服务器都是在国内，不论访问某一张博客图片的人位于国内还是国外。在这里，「源站」指的就是作为图床的这个存储桶，这是因为 CDN 节点上的图片其实都是来自于这个存储桶，或者当我们通过 CDN 提供的域名访问某个图片访问不到时，CDN 也会向这个源站请求图片。「源站类型」不用更改，选择「默认源站」设置即可。

如果存储桶设置的是公有读，那么下面的「回源鉴权」和 「CDN鉴权」不需要开启，否则建议开启「回源鉴权」。

保存这些配置以后，就会看到加速域名正在部署，过几分钟就会部署完毕。此时，进入腾讯云 CDN 的域名管理页面，可以看到已经添加了加速域名：

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/Xnip2020-03-15_23-39-33.jpg" width="800">
</p>

# CDN 设置

腾讯云 CDN 也提供了很多对域名的管理功能，在 「域名管理」页面，点击刚才配置好的域名，可以对域名配置进行修改。在 「访问控制」下面，可以设置 CDN 的防盗链功能，可以在 refer白名单下填上允许访问图片的域名，域名格式是一个模式一行，不用加 `http://`，例如，我的设置是：

```
jdhao.github.io
127.0.0.1
```

# References #

+ https://www.cloudflare.com/learning/cdn/what-is-a-cdn/
+ https://www.cloudflare.com/learning/cdn/glossary/origin-server/
+ [对象存储作为 CDN 源站](https://cloud.tencent.com/document/product/228/37849)
+ [使用 CDN 加速访问对象存储](https://cloud.tencent.com/document/product/436/18668)
+ [对象存储开启域名管理](https://cloud.tencent.com/document/product/436/18424)
+ [腾讯云 CDN 防盗链设置](https://cloud.tencent.com/document/product/228/41454)
