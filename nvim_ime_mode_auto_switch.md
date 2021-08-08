---
title: "Neovim 输入法模式自动切换设置"
date: 2021-02-25T22:21:12+08:00
draft: false
tags: []
categories: [Nvim]
---

Vim/Neovim 下输入中文最大的痛就是需要频繁从中文模式切换到英文模式，来输入 Vim 的各种命令。本文总结在 macOS、Windows 下如何在离开 insert 模式的时候自动切换到英文输入。

<!--more-->


# macOS


## 使用 vim-barbaric

[vim-barbaric](https://github.com/rlue/vim-barbaric) 是一款帮助用户自动设置输入法模式的插件。在 macOS 下，需要额外安装 [xkbswitch-macosx](https://github.com/myshov/xkbswitch-macosx)：

```bash
curl -o /usr/local/bin/xkbswitch https://raw.githubusercontent.com/myshov/xkbswitch-macosx/master/bin/xkbswitch
```

然后安装该插件即可：

```vim
Plug 'rlue/vim-barbaric'
```

该插件开箱即用，无需额外设置。


## 使用 vim-xkbswitch

另外一款插件是 [vim-xkbswitch](https://github.com/lyokha/vim-xkbswitch)，首先安装该插件：

```vim
Plug 'lyokha/vim-xkbswitch'
```

另外该插件依赖 [libxkbswitch-macosx](https://github.com/myshov/libxkbswitch-macosx)，需要安装该库：

```bash
curl -o /usr/local/lib/libxkbswitch.dylib https://github.com/myshov/libxkbswitch-macosx/raw/master/bin/libxkbswitch.dylib
```

最后，在 Neovim 配置中加入下面的设置即可：

```vim
let g:XkbSwitchEnabled = 1
```

# Windows

Windows 下可以安装 [neuims](https://github.com/Neur1n/neuims):

```vim
if g:is_mac
  Plug 'lyokha/vim-xkbswitch'
elseif g:is_win
  Plug 'Neur1n/neuims'
endif
```

然后使用命令 `IMSToggle` 打开这个插件的功能。打开功能以后，使用 `ESC` 离开插入模式，输入法会自动切换到 US keyboard 模式，重新按 i 进入插入模式，会切换到之前的输入法 (注意是切换到了输入法的默认状态，如果输入法默认状态是英文输入，在中文输入状态，按 ESC 离开插入模式，再进入插入模式，输入法处于英文输入状态，目前似乎还不支持切换到输入法上次使用的状态，不过这已经比较好了)。
