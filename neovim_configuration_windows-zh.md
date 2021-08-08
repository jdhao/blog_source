---
title: "Windows 10 系统下 Neovim 安装与配置"
date: 2018-11-16 01:02:37+0800
tags: [Cmder, Windows]
categories: [Nvim]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190119221237.png" width="600">
</p>

本篇文章介绍如何在 Windows 系统下安装并配置终端上的 [Neovim](https://github.com/neovim/neovim)。关于如何配置 Neovim 的 GUI 客户端 [nvim-qt](https://github.com/equalsraf/neovim-qt)，可以参考 [这篇博客](https://jdhao.github.io/2019/01/17/nvim_qt_settings_on_windows/)。

<!--more-->

**TL;DR**: 长时间在 Windows 使用 Neovim 编程以后，我认为要在 Windows 上获得使用 Neovim 的最佳体验，**必须使用 GUI 客户端**（使用 GUI 客户端能避免很多小问题），而不是在终端命令行使用 Neovim。另外，Windows 系统下一些不错的 GUI 客户端有 [Nvim-qt](https://github.com/equalsraf/neovim-qt) 和
[fvim](https://github.com/yatli/fvim)（新出来的一个客户端，体验不错）。

# 开始之前

Windows 自带的 CMD 命令行非常糟糕，不仅颜色难看，而且缺乏许多常用的 Linux 命令。不建议使用 CMD 作为命令行终端，建议使用 [Cmder](https://jdhao.github.io/2017/12/29/cmder-usage/)。Cmder 安装的时候，也附带安装了 [Git for Windows](https://git-scm.com/download/win) 和其他的一些工具，可以使用 Linux 下的常用命令，如 `ls`, `find`, `pwd` 等等。

# 如何安装 Neovim

Neovim 针对各个系统均有编译好的安装包，用户无需自己编译，省去了大量配置时间，安装非常方便。从 [这里](https://github.com/neovim/neovim/releases/download/nightly/nvim-win64.zip) 可以下载到 Neovim win-64 位安装包，直接解压安装包即可完成安装。另外，需要把 Neovim 可执行程序所在的目录添加到系统的 PATH 中，确保在命令行可以使用 `nvim` 这个命令打开 Neovim。

当然也可以用 Chocolatey 或者 scoop 等包管理工具安装 Neovim，具体可以参考 [这里](https://github.com/neovim/neovim/wiki/Installing-Neovim#windows) 的说明。

# 配置

## 配置文件应该放在哪里？

Neovim 使用了和 Vim 不同的配置文件名称以及配置文件存放位置，根据 Neovim [官方文档](https://neovim.io/doc/user/starting.html#base-directories)，Neovim 的配置文件应该命名为 `init.vim`，并且应该放在 `~/AppData/Local/nvim` 目录下[^2]。

## 安装插件管理器 vim-plug

[Vim-plug]() 是一个不错的插件管理器，要安装 vim-plug，首先打开 PowerShell 窗口，然后执行以下的命令：

```powershell
md ~\AppData\Local\nvim\autoload
$uri = 'https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
(New-Object Net.WebClient).DownloadFile(
  $uri,
  $ExecutionContext.SessionState.Path.GetUnresolvedProviderPathFromPSPath(
    "~\AppData\Local\nvim\autoload\plug.vim"
  )
)
```

在 Neovim 的配置文件中，使用如下的 vim-plug 示例配置：

```vim
call plug#begin('~/AppData/Local/nvim/plugged')
" below are some vim plugin for demonstration purpose
Plug 'joshdick/onedark.vim'
Plug 'iCyMind/NeoSolarized'

Plug 'vim-airline/vim-airline'
Plug 'vim-airline/vim-airline-themes'
call plug#end()
```

打开 Neovim，然后运行 `:PlugInstall` 命令，所有要安装的插件都会被安装到目录 `~/AppData/Local/nvim/plugged` 下. 当然，你可以根据自己的实际需求，更改插件安装的目录。

# 问题及解决方案

## 退出 Neovim 窗口的时候，buffer 里面的内容未清空

在命令行使用 Neovim 时，当退出 Neovim 窗口，原来的窗口里面的文字内容仍然显示在命令行，并未被清空，浪费了大量的空间。这和 Neovim 在 Linux 下的表现不相同，如何在退出 Neovim 以后自动清除这些文字内容呢？

经过一番搜索，我发现了一个对 Cmder 有效的解决方法。打开 Cmder 的配置，找到 `Startup -> Environment`，添加如下的配置：

```
set TERM=xterm-256color
```

重启 Cmder，以上的问题应该就消失了。更多讨论，参见[这里](https://vi.stackexchange.com/a/17971/15292).

## 当配置文件出错时，无法进入 Neovim 窗口

在命令使用 Neovim，如果配置文件某行设置有错，在打开 Neovim 时，我们会收到如下提示：

> Press ENTER or type command to continue
>

然而，如果我们按下 <kbd>Enter</kbd>，Neovim 也不会启动。Neovim 程序会卡死在那里，除非你使用 <kbd>Ctrl</kbd>+ <kbd>C</kbd> 强行关闭这个进程。

为了避免这个问题，根据 [这里](https://github.com/neovim/neovim/issues/8133) 的回答，可以使用以下命令启动 Neovim：

```
nvim --cmd silent!
```

## 一些快捷键无法正常工作

### Neovim 和 Cmder 的快捷键冲突

部分原因是 Neovim 和 Cmder 的某些快捷键有冲突，例如在 Cmder 中，`Ctrl-W` 用来关闭一个 console 窗口。但是 `Ctrl-W` 在 Neovim 被用于和窗口相关的操作。可能还存在其他有冲突的快捷键，例如 [`Ctrl-v` 无法像 Linux 下那样工作](https://superuser.com/questions/885601/getting-ctrlv-to-work-in-vim-inside-conemu)。

要禁止 Cmder 中和 Neovim 冲突的快捷键，可以在设置的 `Keys & Macro` 部分更改或者禁止那些冲突的快捷键，

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200214005849.png" width="600">
</p>

### `<Ctrl-6>` 无法工作?

在 Linux 或者 Mac 下，我们可以使用 [`Ctrl-^`(实际上是`Ctrl-6`)](https://stackoverflow.com/a/30450397/6064933) 来切换 Neovim 的 buffer，但是在 Windows Cmder 命令行下面，我发现 `<Ctrl-6>` 不起作用，按下以后也无法切换 buffer。我试着把 `Ctrl-6` 映射为 `Ctrl-^`:

```vim
nnoremap <C-6> <C-^>
```

还是不起作用。目前暂时使用 `Ctrl+shift+6` 来切换 buffer。

**我的完整 Neovim 配置可以在 [这里](https://github.com/jdhao/nvim-config) 查看。关于如何配置 Neovim Python 开发环境，可以看 [这篇文章](https://jdhao.github.io/2018/12/24/centos_nvim_install_use_guide_en/)**。

# 参考链接

+ [Neovim 配置文件应该放在哪里](https://github.com/neovim/neovim/issues/3700)？
+ [设置 Cmder 的终端类型](https://github.com/neovim/neovim/issues/8444)。
+ [Changing Cmder hotkey](https://conemu.github.io/en/SettingsHotkeys.html)
+ [`Ctrl-6` 不起作用](https://github.com/onivim/oni/issues/2002)。
+ [Shift+Insert 不起作用](https://github.com/equalsraf/neovim-qt/issues/327)。
+ [Shift+Insert 不起作用](https://github.com/equalsraf/neovim-qt/issues/253)。

[^1]: It should work for Windows 7 as well.
[^2]: 打开 Neovim，使用 `:echo stdpath('config')` 可以查看该目录的具体位置。
