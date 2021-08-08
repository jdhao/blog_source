---
title: "Neovim Configuration for System-wide Use"
date: 2019-11-10 10:33:29+0800
tags: []
categories: [Nvim]
---

I have been using Neovim for personal projects for over a year. Recently, I
want to set up Neovim system wide so that other users in the server can also
use my config if they want. In this post, I will describe the necessary
configurations.

<!--more-->

# Installation

First, you need to install Neovim in a system location, for example, under
`/opt`. Then edit the system profile `/etc/profile` and add the path of
Neovim:

```
export PATH=/etc/nvim-linux64/bin:PATH
```

# Configuration

## Global cofiguration ##

According to the documentation of Neovim, Neovim will look for
`nvim/sysinit.vim` under `$XDG_CONFIG_DIRS` (the default is `/etc/xdg`)[^1].

So for global configuration, we need to put all configuration under
`/etc/xdg/nvim`. Take my configuration for an example:

```
mkdir /etc/xdg/nvim && cd /etc/xdg/nvim && git clone https://github.com/jdhao/nvim-config.git .
```

We also need to create a symlink `sysninit.vim` and link it to `init.vim`:

```
ln -s init.vim sysinit.vim
```

Change the value of `g:nvim_system_wide` in
[init.vim](https://github.com/jdhao/nvim-config/blob/master/init.vim) to 1 to
install the plugins system-wide.

Since I use vim-plug to manage plugins, I have set up vim-plug to install the
system wide plugin under `/usr/local/share/nvim/site` (see [code
here](https://github.com/jdhao/nvim-config/blob/master/plugins.vim#L28-L36)).
Now, as root user, open neovim and use `:PlugInstall` to install plugins.

The system configuration is finished.

## Personal configuration ##

Ususally, each user may want to change the default configuration set by the
root users. The user's personal config should be put in
`$HOME/.config/nvim/init.vim`. It will override the global configurations.

There is currently one gotcha: the users can not install local plugins using
vim-plug easily. If the user want to install a custom plugin, they should first
clone the plugin repo to a location and add the plugin path to `runtimepath`.
For example, if we install [`ayu-vim`](https://github.com/ayu-theme/ayu-vim)
under `/home/jdhao/.local/share/nvim/plugged/ayu-vim`, we should add the
following setting to `$HOME/.config/nvim/init.vim`:

```
set runtimepath+=/home/jdhao/.local/share/nvim/plugged/ayu-vim
```

Another way of adding your personal plugins to runtimepath is by using
vim-plug:

```
Plug '~/.local/share/nvim/plugged/ayu-vim'
```

You have to manually update the plugin though.

# References #

+ Neovim doc, `:h 'runtimepath'`.
+ Neovim doc, `:h startup`.
+ https://wiki.archlinux.org/index.php/XDG_Base_Directory
+ https://github.com/neovim/neovim/issues/8871
+ [system wide nvim config](https://github.com/neovim/neovim/issues/8994).

[^1]: See neovim documentation `:h sysinit.vim`.
