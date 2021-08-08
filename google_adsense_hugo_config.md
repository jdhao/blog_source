---
title: "谷歌 Adsense 申请及在 Hugo 中的配置"
date: 2019-09-22T12:11:18+08:00
tags: [Hugo, adsense]
categories: [Blog]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/GA-L.jpg", width="600">
</p>

我的博客目前利用 GitHub pages 服务托管，不需要付费，但是博客的图床选用了腾讯云的对象存储，每个月还需要付一笔流量费用。为了保持收支平衡，也为了好玩，决定申请谷歌的 Adsense 一试。本文记录申请 Adsense 以及在 Hugo 博客中配置 Adsense 的过程。

<!--more-->

# Adsense 申请

Adsense 对申请者的网站有一定要求，并不是所有网站申请都能通过，谷歌内部有一套算法来确定网站是否满足条件。所以，要申请 Adsense，博客的文章数量以及博客的访问量要达到一定的标准，如果刚建立的博客，基本上不可能通过。

我在 2017 年的时候申请过一次 Adsense，不过那时候博客的博文太少了，申请没多久，就收到了谷歌的通知：博客不符合 Adsense 开通条件。今年 (2019 年)，博客的文章已经超过 100 篇，我又申请了一次。Adsense 的网站说审核一般会花费 3 天左右的时间，但是我的申请审核显然长了很多，每次登录上去查看进展，都是正在审核，至少过了一周的时间，才成功审核通过。

# 在 Hugo 中配置 Adsense

Hugo 有很多主题，每个主题配置 Adsense 的方式可能不太一样，我的博客目前使用的是[Even](https://github.com/olOwOlo/hugo-theme-even) 主题。下面介绍如何在 Even 主题中加入 Adsense 配置。

Adsense 申请成功以后，会有一段广告代码，类似下面这种：

```
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<script>
  (adsbygoogle = window.adsbygoogle || []).push({
    google_ad_client: "ca-pub-6058871559165202",
    enable_page_level_ads: true
  });
</script>
```

上述代码中的一串数字谷歌分配的 publisher ID，需要改成自己的 ID。把这段代码放到 Even 主题目录下的 `layouts/partials/head.html` 文件里面，然后重新部署。

另外，我们需要创建 `ads.txt` 文件，文件的内容如下：

```
google.com, pub-6058871559165202, DIRECT, f08c47fec0942fa0
```

**注意 `pub-` 后面要改成自己的 publisher ID**。这个文件需要放在博客的根域名下面，如果使用的 Hugo，那么放到 Hugo 博客根目录下的 `public` 目录里即可。

目前谷歌 Adsense 支持设置自动广告：用户不需要自己设定在哪里放广告，Adsense 可以感知你的页面结构，然后自动在合适位置放置广告。刚使用 Adsense 的时候，广告展示可能会没有，或者比较少，到后面逐渐会多起来，可能是Adsense 的策略原因。

# 其他

Adsense 收入达到 10 美元的时候，谷歌会向你邮寄一个verification PIN，你需要在 Adsense 上验证 verification PIN。Adsense 收入达到 100 美元才可以提现。

# 参考 #

+ https://www.qikqiak.com/post/add-adsense-in-hugo-article/
+ https://blog.xu42.com/2018/09/01/google-adsense/
+ [Ads.txt 介绍](https://support.google.com/adsense/answer/7532444?hl=en).
+ [如何找到自己的 Adsense publisher ID](https://support.google.com/adsense/answer/105516).
