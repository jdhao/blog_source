---
title: "Windows 系统下 Python 以及 Pip 的安装和使用"
date: 2015-10-21 14:29:00 +0800
tags: [Windows, pip]
categories: [Python]
---

本文主要介绍在 Windows 系统下，如何安装 Python 和 Pip, 以及 Pip 的简单使用。

<!--more-->

# 安装 Python

可以从官方途径[安装 python](https://www.python.org/downloads/)，安装包比较小，但是这个安装包只包含了运行 Python 所必需的库以及 Python 内置的一些库，因此缺少很多常用的库，例如 Numpy, PIL, Matplotlib 等等，如果需要使用这些库，需要自己手动去安装。

另外一个不错的选择是[直接安装 Anaconda](https://www.anaconda.com/download/)。Anaconda 是科学计算常用的一个 Python 发型版本，包含 Python 的可执行程序，Anaconda 预装了很多常用的 Python 库，例如 Matplotlib，Numpy, 省去了自己安装的麻烦。并且 Anaconda 自带了库管理工具 [`conda`](https://conda.io/docs/), 相当于加强版的 [`pip`](https://pip.pypa.io/en/stable/quickstart/)，非常方便。

请注意，[安装 python](https://www.python.org/downloads/) 以后请把 python 可执行文件的路径加入系统的 [`PATH` 变量](https://stackoverflow.com/questions/23400030/windows-7-add-path)，以便后面可以直接在命令行使用 python 命令。

# 安装 pip

安装 Python 以后（我的 Python 版本是 32 位，版本号 2.7.10），如果需要安装一些其他的库，一般有两种办法，一种是自己手动去各个库的官网下载，自己安装, 这种方式十分麻烦，有的库可能还需要编译，如果不熟悉，很容易弄错；另一种方法是安装 pip，使用 pip 可以方便安装各种 Python 库。

## 你是否需要安装 pip？ (updated: 2018-01)

根据 pip 文档介绍，如果 python 版本满足 `Python 2 >=2.7.9 or Python 3 >=3.4`，那么安装的 Python 已经自带了 pip 工具，不要单独安装 pip。

在命令行使用 `python --version` 查看系统安装的 Python 版本来确定你是否需要安装pip。

## 使用 `get-pip.py` 安装 pip

首先，[下载 `get-pip.py`](https://bootstrap.pypa.io/get-pip.py) 文件，然后[在命令行中进入该文件下载的目录](https://stackoverflow.com/questions/17753986/how-to-change-directory-using-windows-command-line)，执行下面的命令

```
python get-pip.py --user
```

pip 就安装好了。

# pip 的使用

+ 要安装某个python库，直接使用 `pip install <package_name>` 即可。如 `pip install matplotlib`
+ 要查看 python 都安装了哪些库，可以使用 `pip list`
+ 要删除某个安装的库，可以使用 `pip uninstall <package_name>` 命令
+ 要显示某个库的详细信息，可以使用 `pip show <package_name>` 命令。如 `pip show numpy`，结果如下

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-23/24227308.jpg"
         title="显示某个库的信息"
         style="float: middle;">

+ 显示安装的库中有哪些是需要升级的，可以使用 `pip list -–outdated`，显示如下：

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-23/39092266.jpg"
         title="show outdated package"
         style="float: middle;">

+ 升级一个已经安装的package，使用 `pip install --upgrade <package_name>`，例如 `pip install –-upgrade numpy`

# 参考

+ <https://stackoverflow.com/questions/4750806/how-do-i-install-pip-on-windows>
+ <https://pip.pypa.io/en/latest/quickstart/>
