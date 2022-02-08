---
title: "解决 GitHub 资源无法下载的问题"
date: 2021-01-08T23:38:43+08:00
draft: false
tags: [GitHub]
categories: [Note]
---

<details>
<summary><font size="2" color="red">更新记录</font></summary>

+ 2022-02-08： [fasgit hub 被 GFW 封了](https://t.me/fastgitchannel/81)，换一个地址。
</details>

GitHub 虽然没被封，但是阻断严重，下载 GitHub 资源速度奇慢无比，总结一些验证可行的方法。

<!--more-->

# 加速下载 github.com 资源

可以利用 [fastgit](https://fastgit.org/) 加速 github.com 的下载。原有下载命令：

```
wget https://github.com/neovim/neovim/releases/download/nightly/nvim-linux64.tar.gz
```

新命令：

```
wget "https://hub.fastgit.xyz/neovim/neovim/releases/download/nightly/nvim-linux64.tar.gz"
```

把 `github.com` 替换为 `hub.fastgit.xyz` 即可，也支持 git clone 加速。

# 下载 githubusercontent.com 的内容

## 使用 fastgit

使用上面提到的 fastgit 也可以加速下载 githubusercontent.com 的内容。原始命令：

```
wget https://raw.githubusercontent.com/jdhao/nvim-config/master/docs/Nvim_setup.sh
```

新命令：

```
wget https://raw.fastgit.org/jdhao/nvim-config/master/docs/Nvim_setup.sh
```

需要把链接中 `githubusercontent.com` 替换为 `fastgit.org`。

## 使用 jsdelivr

可以使用 jsdelivr 的 cdn 访问对应的资源，亲测可行。需要对源地址进行一些改造，换成 jsdelivr 上对应的 URL 即可。例如，源地址：`https://raw.githubusercontent.com/jdhao/nvim-config/master/docs/Nvim_setup.sh`
对应 jsdelivr 地址：`https://cdn.jsdelivr.net/gh/jdhao/nvim-config@master/docs/Nvim_setup.sh`。`@master` 后面跟的是该文件对应的 master branch，如果是默认的 master，那么也可以不用加 `@master`，否则需要加上分支名称。

## 更改系统 hosts 文件

更改系统 hosts 文件 (在 Linux 上需要有 root 权限)，编辑 `/etc/hosts`， 加入一条 host 记录：

```
199.232.96.133 raw.githubusercontent.com
```

上面的 IP 地址是 `githubusercontent.com` 对应的 ip 地址之一，可以在 [这里](https://www.ip138.com/) 查看。

# 使用第三方辅助工具

## dev-sidecar

[dev-sidecar](https://github.com/docmirror/dev-sidecar)，用 Electron 写的一个工具， 可以帮助用户加速 github，stackoverflow 等网站的访问，可以一试。

# 参考

+ https://www.wolfdan.cn/jsdelivr%E5%8A%A0%E9%80%9F%E8%B5%84%E6%BA%90%E5%8A%A0%E8%BD%BD-raw-github%E8%A2%ABban%E4%B9%8B%E5%90%8E%E5%A6%82%E4%BD%95%E8%AE%BF%E9%97%AEgithub%E8%B5%84%E6%BA%90/
