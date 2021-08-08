---
title: "Neovim configuration on Windows 10"
date: 2018-11-15 18:08:00 +0800
tags: [Cmder, Windows]
categories: [Nvim]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190119221237.png" width="600">
</p>

<font color="blue">update: 2020-11-15, add detailed instruction for Neovim config location.</font>

**TL;DR**: After nearly one year of using Neovim on Windows, I find that the
best experience one can get on Windows is to use GUI Neovim client such as
[Nvim-qt](https://github.com/equalsraf/neovim-qt) or [fvim](https://github.com/yatli/fvim),
not the terminal Nvim[^2].

In this post, I want to share how to install and configure Neovim on Windows
10[^1]. For configurations of [nvim-qt](https://github.com/equalsraf/neovim-qt)
on Windows, check [this post](https://jdhao.github.io/2019/01/17/nvim_qt_settings_on_windows/).

<!--more-->

# Before we begin

The builtin CMD on Windows is awful and lacks features of a normal Linux
terminal. I strongly recommend readers using a better terminal emulator, for
example, [Cmder](https://jdhao.github.io/2017/12/29/cmder-usage/), which
comes with [Git for Windows](https://git-scm.com/download/win) and other
utilities for you.

# Install

You can download [the neovim nightly release](https://github.com/neovim/neovim/releases/download/nightly/nvim-win64.zip)
from its GitHub repo. Then extract the package, add the extracted folder to
your system PATH variable, and make sure that you can invoke `nvim` on the
command line.

See [here](https://github.com/neovim/neovim/wiki/Installing-Neovim#windows) for
other ways to install Neovim on Windows.

# Configuration

## Where to put the configuration file?

A common confusion for novice users of Neovim is that they do not know where to
put the config files. It is in fact quite simple.

First of all, the neovim config file is named `init.vim` regardless of your
system.

Second, based on Neovim [official documentation](https://neovim.io/doc/user/starting.html#base-directories), you should put `init.vim`
under the directory `~/AppData/Local/nvim` on Windows. To find here that
directory is exactly, use the command `:echo stdpath('config')` inside Neovim.

If this directory does not exist, do not worry. Just create it and put your
config file there.

## Install plugin-manager vim-plug

 [vim-plug](https://github.com/junegunn/vim-plug) is popular plugin manager for
 Neovim. To install it on Windows, open a **PowerShell** terminal (**not
 Windows CMD!**), and execute the following command:

```
md ~\AppData\Local\nvim\autoload
$uri = 'https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
(New-Object Net.WebClient).DownloadFile(
  $uri,
  $ExecutionContext.SessionState.Path.GetUnresolvedProviderPathFromPSPath(
    "~\AppData\Local\nvim\autoload\plug.vim"
  )
)
```

In `init.vim`, use the following settings for vim-plug:

```vim
call plug#begin('~/AppData/Local/nvim/plugged')
" below are some vim plugins for demonstration purpose.
" add the plugin you want to use here.
Plug 'joshdick/onedark.vim'
Plug 'iCyMind/NeoSolarized'

Plug 'vim-airline/vim-airline'
Plug 'vim-airline/vim-airline-themes'
call plug#end()
```

When we run command `:PlugInstall`, all the plugins will be installed under
`~/AppData/Local/nvim/plugged`. We can also change this directory to where we
want, for example, `~/AppData/Local/nvim-data/plugged`.

# Issues

## The Neovim window content is not cleared when I exit

When I exit Neovim, the text content inside the buffer is still shown in the
command line, wasting a lot of space. How do we clear the Neovim window content
after exit?

After some research, I have finally found a solution that works for Cmder. Open
Cmder settings and go to `Startup -> Environment`, add the following settings:

```
set TERM=xterm-256color
```

Restart Cmder and the issue should disappear. More discussions can be found
[here](https://vi.stackexchange.com/a/17971/15292).

## Can not enter Neovim if there are errors in config file

If there are errors in `init.vim`, Neovim will prompt you the following message
during startup:

> Press ENTER or type command to continue
>

However, pressing <kbd>Enter</kbd> has no effect. Neovim will hang unless you
press `Ctrl-C` to terminate the process.

To circumvent this issue, according to
[this issue](https://github.com/neovim/neovim/issues/8133), try to open Neovim with
the following option:

```
nvim --cmd silent!
```

## Some shortcuts do not work

### Shortcut conflict with Cmder

For Cmder, `Ctrl-W` is used to close a console. Unfortunately, this shortcut is
also used by Neovim as prefix for window operations. Maybe there are some other
conflicting shortcuts,
for example, [Ctrl-V does work like Linux](https://superuser.com/q/885601/736190).

To disable the conflicting shortcuts in Cmder, go to `Keys & Macro` part to
change or disable a shortcut.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200214005849.png" width="600">
</p>

### `<Ctrl-6>` does not work?

On Linux, we can use [`Ctrl-^`(actually
`Ctrl-6`)](https://stackoverflow.com/a/30450397/6064933) to switch buffers.
However, on Windows, using `<Ctrl-6>` for buffer switching does not work in
terminal nvim. I try to map `Ctrl-6` to `Ctrl-^`:

```vim
nnoremap <C-6> <C-^>
```

It doesn't work either. Until I find the solution , I use `Ctrl+shift+6` to
switch buffers.

My complete configurations for Neovim can be found in [this repo](https://github.com/jdhao/nvim-config).
For How to configure Neovim for
Python development, check [this post](https://jdhao.github.io/2018/12/24/centos_nvim_install_use_guide_en/).

# References

+ [Where to put Neovim config file](https://github.com/neovim/neovim/issues/3700).
+ [Set terminal type for Cmder](https://github.com/neovim/neovim/issues/8444).
+ [Changing Cmder hotkeys](https://conemu.github.io/en/SettingsHotkeys.html)
+ [`Ctrl-6` does not work](https://github.com/onivim/oni/issues/2002)
+ [Shift+Insert does not work](https://github.com/equalsraf/neovim-qt/issues/327)
+ [Shift+Insert does not work](https://github.com/equalsraf/neovim-qt/issues/253)

[^1]: It should work for Windows 7 as well.
[^2]: Maybe the situation will change after Microsoft's [windows terminal](https://github.com/microsoft/terminal) become mature enough.
