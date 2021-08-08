---
title: "Boosting Your Productivity on Terminal with Zsh and Plugins"
date: 2019-02-19 00:27:53+0800
tags: [shell, Zsh]
categories: [Linux]
---

In my [previous
post](https://jdhao.github.io/2018/10/13/centos_zsh_install_use/), I have
introduced how to build Zsh from source and install
[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) to enhance your terminal
experience. In this post, I'd like to show you how to manage plugins with
[zplug](https://github.com/zplug/zplug) and a few more plugins to boost your
productivity.

<!--more-->

# Plugin management with zplug

Oh-my-zsh has a lot of builtin plugins and works great. But there are also a
lot of plugins which are not packaged with oh-my-zsh. You can [manually add a
plugin
*foo*](https://github.com/robbyrussell/oh-my-zsh/wiki/Customization#adding-a-new-plugin)
to the oh-my-zsh custom folder and activate it in `.zshrc` like the following:

```zshrc
plugins=(
  [...other plugins...] foo
)
```

The above method works but becomes tedious once you need to install several
external plugins. This is when plugin manager comes to help.

Just like what you see in Vim, several competing plugin managers are out there
for Zsh users, such as [antigen](https://github.com/zsh-users/antigen),
[zplug](https://github.com/zplug/zplug),
[antibody](https://github.com/getantibody/antibody),
[zgen](https://github.com/tarjoilija/zgen)[^1]. I decided to use zplug for its
boasted speed and its versatility.

It is very easy to install:

```bash
curl -sL --proto-redir -all,https https://raw.githubusercontent.com/zplug/installer/master/installer.zsh | zsh
```

Then add the following backbone setting for zplug in `.zshrc`:

```zshrc
# the backbone of zplug
source ~/.zplug/init.zsh

# plugins installation goes below

zplug load --verbose
```

All the plugin installation settings should be put inside the backbone code.
After adding a plugin, **do not forget** to source `.zshrc`. To install
plugins, use command `zplug install` on the command line. Please consult the
[zplug doc](https://github.com/zplug/zplug#usage) for more usage.

# Recommended plugins

Below I will introduce several plugins and themes I find useful.

## Auto suggestions

Plugin [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)
will suggest you the command you want to use based on your command history,
making it quicker to execute your frequent command.

Install it with zplug:

```
# command auto-suggestion based on history
zplug "zsh-users/zsh-autosuggestions"
```

## Faster directory navigation

[z.lua](https://github.com/skywind3000/z.lua) is plugin which makes directory
navigation faster. Install it with zplug:

```
zplug "skywind3000/z.lua"
```

For its settings and usage, see
[here](https://jdhao.github.io/2019/02/14/fast_dir_navigation_zlua/).

## Vim mode

In zsh, we can use the [vi mode](https://dougblack.io/words/zsh-vi-mode.html)
for faster command editing. Vi is activated by following command:

```
bindkey -v
```

To go to NORMAL mode, press <kbd>ESC</kbd>. Basic vi motion and change
oprations are provided for you to manipulate the command. Unfortunately, there
is no visual indication which mode you are in, making it hard to use the vi
mode.

The plugin [zsh-vim-mode](https://github.com/softmoth/zsh-vim-mode) solves this
issue by adding the mode indicator in the `RPS1`[^2]. It also adds a few
enhanced functions to tweak the vim mode.

First, install it with zplug:

```
# enhanced zsh vim mode
zplug "softmoth/zsh-vim-mode"
```

You can [customize the cursor
shape](https://github.com/softmoth/zsh-vim-mode#mode-sensitive-cursor-styling)
with this plugin, for example:

```
MODE_CURSOR_VICMD="green block"
MODE_CURSOR_VIINS="#20d08a blinking bar"
MODE_CURSOR_SEARCH="#ff00ff blinking underline"
```

To add the current mode indicator on the right prompt, add the following the
setting:

```
MODE_INDICATOR_VIINS='%F{15}<%F{8}INSERT<%f'
MODE_INDICATOR_VICMD='%F{10}<%F{2}NORMAL<%f'
MODE_INDICATOR_REPLACE='%F{9}<%F{1}REPLACE<%f'
MODE_INDICATOR_SEARCH='%F{13}<%F{5}SEARCH<%f'
MODE_INDICATOR_VISUAL='%F{12}<%F{4}VISUAL<%f'
MODE_INDICATOR_VLINE='%F{12}<%F{4}V-LINE<%f'
```

The effect is shown below:

![](https://blog-resource-1257868508.file.myqcloud.com/20190219004706.png)

### Minor issues ###

It should be noted that in order to use the surround object provided by this
plugin, the value of environment variable `KEYTIMEOUT` [should not be too
low](https://github.com/softmoth/zsh-vim-mode/issues/13).

Another issue with this plugin is that it changes the default behaviour of
<kbd>Home</kbd> and <kbd>End</kbd> keys: pressing Home will run help for the
current command and pressing End will escape the command line into normal mode.
To fix this issue, you need to bindkey Home and End key specifically:

```
bindkey '^[[H' beginning-of-line
bindkey '^[[F' end-of-line
```

The above settings should be put after the position where this plugin is loaded
to take effect.

## Syntax highlight

The plugin
[fast-syntax-highlighting](https://github.com/zdharma/fast-syntax-highlighting)
will highlight your command on the command line, making it easy to distinguish
between different parts of the command. To install it, add the following
setting:

```
# defer means to load this plugin after all the other plugins
zplug "zdharma/fast-syntax-highlighting", defer:2
```

# Conclusions

In this post, I have introduced zplug --- the plugin manager for Zsh and a few
useful plugins. For a more comprehensive list of zsh plugins, you may find
[this awesome zsh list](https://github.com/unixorn/awesome-zsh-plugins) useful.
As a concluding remark: you should choose plugins wisely and only install those
you truly need.

# References

+ [How to indicate vim mode on command line](https://unix.stackexchange.com/questions/547/make-my-zsh-prompt-show-mode-in-vi-mode/452529#452529)
+ [awesome zsh](https://github.com/unixorn/awesome-zsh-plugins)
+ [Zsh bindkey and keymaps](http://zsh.sourceforge.net/Doc/Release/Zsh-Line-Editor.html#Zle-Builtins)
+ [Zsh parameters](http://zsh.sourceforge.net/Doc/Release/Parameters.html).

[^1]: More plugin managers and frameworks can be found [here](https://github.com/unixorn/awesome-zsh-plugins#frameworks). Choose the one you feel is best.
[^2]: This prompt is displayed on the right-hand side of the screen when the primary prompt is being displayed on the left.
