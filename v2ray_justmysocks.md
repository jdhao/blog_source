---
title: "Justmysocks 支持 v2ray 协议了！"
date: 2020-05-22T00:40:58+08:00
tags: []
categories: [Note]
---


two meetings 即将或者正在召开，上个谷歌也越来越难，最近墙对各种工具的阻断非常严重，连之前[使用了 obfs 的 ss](https://jdhao.github.io/2020/04/25/shadowsocks_obfs_setup/) 都不行了，最近一段时间也处于时断时续的状态。

Justmysocks 之前一直用的都是 shadowsocks 协议，对墙的反制能力不行，每到关键时候就显露疲态。好消息是，justmysocks 终于在最近推出了基于 [v2ray](https://www.v2ray.com/en/) 协议的线路。

<!--more-->

v2ray 相对于 shadowsocks，隐蔽性更好，所以也更加稳定。由于 justmysocks 的 v2ray 目前刚推出，只能手动设置，具体如何设置，可以搜索 v2ray 设置教程。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200522012607.png" width="800">
</p>

v2ray 的客户端也很多选择，完整列表见[这里](https://www.v2ray.com/awesome/tools.html)。列举一些常见的客户端：

+ Windows: [V2rayN](https://github.com/2dust/v2rayN)
+ Mac: [v2rayU](https://github.com/yanue/V2rayU), [ClashX](https://github.com/yichengchen/clashX), [Qv2ray](https://github.com/lhy0403/Qv2ray).
+ Android: [v2rayNG](https://github.com/2dust/v2rayNG/releases), [BifrostV](https://apkpure.com/bifrostv/com.github.dawndiy.bifrostv).

**更新： 2020-05-28**

近期 justmysocks 又推出了一个支持 v2ray 的 s801 节点，参见下图

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200528224209.jpg" width="600">
</p>

官方的说法是这个节点真正计算的数据量是实际使用数据量的 1/10，但是这个节点只是友情提供，不保证质量，也不提供任何形式的服务，并且随时可能会被下架。见 [这里](https://justmysocks.net/members/knowledgebase.php?action=displayarticle&id=13) 说明。

**更新： 2020-06-19**

今天查看 justmysocks 的网站，3-5 三个服务器都已经变为 v2ray 协议了，三个服务器的配置都是完全一样的。估计 justmysocks 所有节点以后都会转为 v2ray 协议。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200619004423.png" width="400">
</p>


# 参考

+ https://www.youtube.com/watch?v=A3R7j_dpGLc
+ https://medium.com/@kissmk3515/2020%E6%9C%80%E5%A5%BD%E7%9A%84v2ray%E6%9C%BA%E5%9C%BA%E8%AF%84%E6%B5%8B-b76a075e4f26
