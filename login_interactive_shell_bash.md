---
title: "为什么 .bash_profile 里面的设置不生效？"
date: 2018-09-06T00:12:53+08:00
tags: [shell, Bash]
categories: [Linux]
---

最近安装了 [Neovim](https://github.com/neovim/neovim)，并且在 HOME 目录下的 `.bash_profile` 中设置好了 Neovim 可执行程序对应的 PATH 变量。奇怪的是，每次在 SSH 客户端打开一个新的服务器连接 session，登录服务器，进入自己账号以后，位于 `.bash_profile` 中的 PATH 设置命令没有生效，导致我无法启动安装的 Neovim，每次登录以后，我还必须手动 source 一下 `.bash_profile` 文件，然后才能使用 nvim 命令，非常让人恼火。本文记录我排查问题的过程以及最终的解决方案。

<!--more-->

首先，我尝试把 `.bash_profile` 里面的设置拷贝到 `.bashrc` 中，发现登录服务器以后 PATH 变量设置是有效的，可以正常使用 Neovim，这说明 `.bash_profile` 里面的设置在登录的时候并未被执行。

原因何在？知道了问题症结以后，很快找到了答案，原来是登录账户时使用的 `su $USERNAME` 指令的问题。我们的服务器登陆方式比较特殊，并不是通过 SSH 直接登陆到自己目标服务器，而是先 SSH 登陆到一个跳板机，然后以一个统一身份登陆到目标服务器，最后每个用户使用 `su $USERNAME` 切换到自己的账号。如果使用该指令切换到自己的账户，打开的 shell 是 non-login shell，只有 `.bashrc` 里面的设置被 source，所以 `.bash_profile` 里面的设置才没有生效。

什么是 non-login shell？要说清这个问题，需要先介绍 shell 的不同工作模式，为了简化问题，仅仅针对 Bash。shell 分为 login shell 和 non-login shell；也可以分为 interactive shell 和 non-interactive shell.

# login/non-login shell 以及 interactive/non-interactive shell

Login shell 一般指的是用户通过正常的 ssh 登录到服务器（输入用户名和密码）进入的 shell；如果进入 login shell 以后，再打开 GUI terminal 或者执行 `bash` 命令，此时打开的是 non-login shell。

Interactive shell 指的是 shell 可以输出结果，也可以接受输入；non-interactive shell 则无法接受用户输入，当运行 script 的时候，实际上就是有一个 non-interactive shell 在执行脚本文件。

# 哪些配置文件被读取？

采用 interactive login shell 登录的时候，首先读取并执行 `/etc/profile` 文件中命令，然后再依次顺序寻找 `~/.bash_profile`,`~/.bash_login` 和 `~/.profile` 文件，然后从第一个存在且可读取的文件中读取并执行指令。Bash 的 man page 说明如下：

> When bash is invoked as an **interactive login shell**, or as a non-interactive shell with the --login option, it first reads and executes commands from the file /etc/profile, if that file exists. After reading that file, it looks for ~/.bash_profile, ~/.bash_login, and ~/.profile, in that order, and reads and executes commands from **the first one that exists and is readable**

如果是 interactive non-login shell, 启动时候，首先读取执行 `/etc/bashrc`，然后再读取执行 `~/.bashrc`。

以上两种情况是最常见的。

使用 `su $USERNAME` 切换到自己的账号，打开的 Bash shell 实际上就是一个 interactive non-login shell，`.bash_profile` 里面的内容并没有被执行，所以设置才没有生效。想要在登录账号的时候确保 `.bash_profile` 里面的配置生效，打开的 shell 必须是 login shell。`su` 命令提供了 `-l` 选项（等同于 `-`）:

> -l      Simulate a full login.  The environment is discarded except for HOME, SHELL, PATH, TERM, and USER.  HOME and SHELL are modified as above.  USER is set to the target login.  PATH is set to `/bin:/usr/bin`.  TERM is imported from your current environment.  The invoked shell is the target login's, and su will change directory to the target login's home directory.

简单来说，`-l` 或 `-` 保证我们打开的是一个 login shell。所以，登录时候使用 `su - USERNAME` 即可保证 `.bash_profile` 里面的设置生效。

# 参考

+ [Why ~/.bash_profile is not getting sourced when opening a terminal](https://askubuntu.com/q/121073/768311)?
+ [.bash_profile not sourced when running su](https://unix.stackexchange.com/q/29791/221410).
+ [Why doesn't my ~/.bash_profile work](https://unix.stackexchange.com/q/88106/221410)?
