---
title: "使用代理加速 Mac 终端下载速度"
date: 2019-10-10 23:17:29+0800
tags: [shell]
categories: [Mac]
---

在 Mac 的终端 (terminal) 下载文件或者 clone git 仓库的时候，明显感觉速度特别慢，即使把梯子的全局模式打开也无济于事，需要在终端设置代理才能加快下载速度，本文简要总结需要的配置。

<!--more-->

在 Unix 终端，有三个和代理相关的环境变量，分别为 `http_proxy`, `https_proxy` 和`all_proxy`。粗略来说，`http_proxy` 和 `https_proxy` 分别用来设置 http 和 https连接的代理，`all_proxy` 设置所有连接的代理。

对于 git clone，由于连接一般是 https 连接，可以设置 `https_proxy` 或者`all_proxy` 变量，设置 `http_proxy` 没有作用。

一般来说代理可以是 http 代理，也可以是 socks5 代理，格式如下:

```
export https_proxy=127.0.0.1:1087  # http 代理
export https_proxy="socks5://127.0.0.1:1087"  # socks5 代理
```

我目前使用的 [梯子](https://github.com/shadowsocks/ShadowsocksX-NG/releases)，支持 socks5 协议，也支持 http 协议，打开 `偏好设置  --> 高级` 选项，可以看到socks5 协议的 ip 以及端口，在 `http` 选项卡，可以看到 http 代理的信息。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20191011001445.png" width="400">
</p>

设置好代理以后，可以使用 `curl cip.cc` 查看当前自己的 ip 信息，确认代理是否设置成功。

# References #

+ https://askubuntu.com/questions/583797/how-to-set-a-proxy-for-terminal
+ https://askubuntu.com/questions/24116/what-is-the-all-proxy-environment-variable-used-for
+ [让终端走代理的几种方法](https://blog.fazero.me/2015/09/15/%E8%AE%A9%E7%BB%88%E7%AB%AF%E8%B5%B0%E4%BB%A3%E7%90%86%E7%9A%84%E5%87%A0%E7%A7%8D%E6%96%B9%E6%B3%95/)
+ [确定自己的 public ip](https://askubuntu.com/questions/95910/command-for-determining-my-public-ip)
+ [mac 终端 proxy 设置](https://github.com/mrdulin/blog/issues/18)
+ [set up proxy in mac terminal](https://stackoverflow.com/questions/37231204/osx-proxy-issue-with-homebrew-install)
