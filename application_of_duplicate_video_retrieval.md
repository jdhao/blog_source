---
title: "基于内容的视频检索/视频指纹的应用"
date: 2021-07-24T21:43:31+08:00
draft: false
tags: [video, retrieval]
categories: [Note]
---

基于内容的视频检索（或视频指纹），是指根据视频内容，从数据库中找到和当前视频在画面上重复高的视频。

<!--more-->

主要应用场景：

+ 版权保护相关：用于保护原创视频内容，及时发现自己的视频是否被第三方盗用，淘宝[^1]，YouTube[^2]，抖音[^3]等都有相关的功能
+ 视频去重：多用于推荐或者信息流场景，去掉重复的视频；云存储提供商，通过视频去重，也能节省存储资源（例如不同用户上传了同一段视频，如果 A 是 B 的一部分，可以不用存储 A）
+ 广告效果追踪：广告类的视频，发布者希望传播的越广越好，通过视频指纹，广告原创者可以追踪视频已经到达了哪些受众，进行效果传播分析
+ 视频溯源：通过找到相似视频，找出视频在互联网上流转的路径有哪，找到视频发布者或者视频源头
+ 互动媒体：通过视频指纹，电视能够检测当前播放的视频，根据编程，提供个性化服务
+ 打击犯罪：用于追踪一些违法内容的传播，例如恐怖主义内容，儿童色情视频等。


# 参考

+ https://en.wikipedia.org/wiki/Digital_video_fingerprinting
+ 谷歌在 content ID 上投入超 1 亿美元：https://www.blog.google/outreach-initiatives/public-policy/protecting-what-we-love-about-internet-our-efforts-stop-online-piracy/
+ YouTube content ID intro: https://support.google.com/youtube/answer/2797370?hl=en
+ content ID turns three: https://blog.youtube/news-and-events/content-id-turns-three
+ 字节跳动“灵识系统”：https://smart.huanqiu.com/article/9CaKrnKfmL2
+ 字节跳动，以算法推动的媒体进阶之路: https://www.jingpt.com/article/41663b6b-3dbb-4efe-bfd0-86a5c947b5c8
+ [视频也能提取“指纹” 人工智能破解行业盗版难题](http://trb.mofcom.gov.cn/article/zuixindt/201712/20171202687244.shtml) （五分钟视频 10 s 内完成特征提取）
+ aliyun 视频检索
    + https://ai.aliyun.com/vi/dna
    + https://www.aliyun.com/activity/intelligent/media-ai-portal?spm=5176.cnmultimediaai.0.0.592e2a51KFBlJf#/videoFingerprinting
+ 百度云视频检索：https://cloud.baidu.com/product/aimms.html

[^1]: [数十万商品短视频排队盖戳 阿里推“视频指纹”技术保护商家](https://www.jiqizhixin.com/articles/2019-06-13-10)
[^2]: YouTube 版权保护系统名为 content ID，具体参考 [这里](https://en.wikipedia.org/wiki/Content_ID_(system)) 和 [这里](https://www.techbang.com/posts/21674-how-does-youtube-catch-your-movie-contains-copyrighted-music-and-video-content-parse-the-content-id-recognition-system-theory-and-function-of).
[^3]: [短视频版权是非多，技术出手来护卫](http://www.cac.gov.cn/2019-05/28/c_1124549891.htm)
