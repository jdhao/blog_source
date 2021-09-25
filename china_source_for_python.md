---
title: "Anaconda Python 和 pip 国内源/镜像"
date: 2021-01-17T21:32:39+08:00
draft: false
tags: [pip]
categories: [Python]
---

使用国外的源下载 Python 发行版和各种 Python 库，速度实在是太慢了😭😠，国内的源速度会快很多，权宜之计。

<!--more-->

# Anaconda 国内源

在国内，从 Anaconda 或者 Miniconda 官方地址下载 Anaconda 或 miniconda 发行版都非常慢，还可能会断掉。

+ Anaconda 安装包清华源地址：https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/
+ Miniconda 清华源地址：https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/

使用 conda 安装 Python 库的时候，也可以使用清华源，打开 `$HOME/.condarc`，添加下面的配置

```yaml
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

# pip 使用国内源

pip 下载库的时候，默认使用 pypi 的官方源，速度不理想。把 pip 的源也改为清华源，速度飞快。

永久方案：

```bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

临时方案（使用清华源安装某些库）

```bash
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple some-package
```

# 参考

+ https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html
+ https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/
+ https://mirrors.tuna.tsinghua.edu.cn/help/pypi/
