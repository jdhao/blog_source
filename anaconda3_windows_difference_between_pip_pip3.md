---
title: "What Is The Difference between pip, pip3 and pip3.6 Shipped with Anaconda3?"
date: 2018-11-19 16:30:00 +08:00
tags: [Windows, Anaconda, Python, pip]
categories: [Note]
---

On Windows system, if you install Python3 via
[Anaconda](https://www.anaconda.com/download/#windows). Under Anaconda root directory,
there is a directory named `Scripts`, in which `pip.exe`, `pip3.exe` and
`pip3.6.exe` all exist. When we want to install a package, a natural question
arises, is there any difference if I use `pip install PACKAGE` or `pip3 install
PACKAGE`?

<!--more-->

# On Windows

The three executables have exactly the same file size and file last-mod time:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200430135213.png" width="800">
</p>

If you show their version information, the output is also exactly the same. On
my system, the output is:

> pip 18.1 from d:\anaconda\lib\site-packages\pip (python 3.6)

So, on Windows, the three executables have no difference.

# What about Linux?

On Linux, the default python is usually Python 2. If you are a system
administrator and install `python2-pip` package, you will see that `pip` and
`pip2` under `/usr/bin` are the same. If you also installed `python3-pip`,
there will an executable named `pip3` under `/usr/bin`. Under this situation,
`pip` and `pip2` is used for installing Python 2.X packages and `pip3` is used
for Python 3.

You can also verify this by using `pip[3] --version`. On my system (Ubuntu on
Windows), the result of `pip --version` is:

> pip 9.0.1 from /usr/lib/python2.7/dist-packages (python 2.7)

, while the output of `pip3 --version` is;

> pip 9.0.1 from /usr/lib/python3/dist-packages (python 3.6)

It is clear that they are different.

# References

+ https://www.reddit.com/r/learnpython/comments/4j1f3x/do_i_use_pip_or_pip3_or_pip34_to_install_packages/
+ https://stackoverflow.com/questions/40832533/pip-or-pip3-to-install-packages-for-python-3
