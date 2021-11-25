---
title: "Setting up True Color Support for Neovim and Tmux on Mac"
date: 2018-10-19 01:08:00 +0800
tags: [Tmux, Nvim, terminal]
categories: [Mac]
---

<details>
<summary><font size="2" color="red">Update log</font></summary>

<font color="blue">2021-11-23: update tmux and nvim version to the latest and fix other issues.</font>
</details>

In this post, I want to share how to set up true color support in Tmux, Nvim
and the shell to make them work nicely together.

<!--more-->

There are tons of posts on the Internet regarding how to set up true color
support to make Tmux, Nvim and terminal work together. Time flies and settings
may change. Many people also do not really understand the reason behind all the
settings and often give some ad-hoc settings. All these lead to chaos. It turns
out that setting up true color support is painfully difficult and costs me
several hours.

# Version info

Before we begin, I will list the info about all the necessary components so
that you can reproduce on your device:

+ Terminal: [iTerm2](https://github.com/gnachman/iTerm2) and [kitty](https://github.com/kovidgoyal/kitty), which support True color by default.
+ Tmux: Version 3.2 (`tmux -V`) and installed via HomeBrew
+ Neovim: v0.5.1 (`nvim --version`) and installed via HomeBrew

First, we have to understand that not all terminals support true colors. Whether
you use a terminal emulator to connect to remote host or use a native terminal,
make sure that your terminal supports true colors. For a list of terminals
supporting true colors, see [this wonderful repo](https://github.com/termstandard/colors).

# Setting up true color support

## terminal emulator settings

### iTerm2 settings

For iTerm2, make sure the output of `echo $TERM` is `xterm-256color`. If the
output is wrong, you should set it properly in iTerm2. Open the iTerm2
preference settings, go to `Profiles --> Terminal`, in the *Terminal Emulation*
part, choose `xterm-256color` as the reported terminal type.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200222134641.jpg">
</p>

**Do not set TERM variable in your shell profile!** It is your terminal
emulator that is responsible for reporting what kind of terminal it supports.
If you set TERM variable in your shell, the TERM variable inside Tmux will also
be set to that value when the shell is initialized, which is simply wrong![^2].
**Do not do this.**

### kitty settings

Open `~/.config/kitty/kitty.conf` and use the following setting:

```
term xterm-256color
```

## Neovim settings

Neovim has good support for true colors. According to [official doc](https://github.com/neovim/neovim/wiki/FAQ#how-can-i-use-true-color-in-the-terminal),
to enable true color support, you need to add the following setting to
`init.vim`:

```vim
set termguicolors
```

## Tmux settings

In order to make Nvim colorscheme work well inside Tmux, you need to add these
settings to your `.tmux.conf`[^1]:

```
set -g default-terminal "screen-256color"
# tell Tmux that outside terminal supports true color
set -ga terminal-overrides ",xterm-256color*:Tc"

# for tmux 3.2, you can use the following setting instead:
# set -as terminal-features ",xterm-256color:RGB"
```

You can also use `tmux-256color` instead of `screen-256color`. `Tmux-256color`
supports italics inside Tmux. In case your system does not have an entry for
`tmux-256color`, you can download terminfo database and build a more complete
one:

```bash
curl -LO http://invisible-island.net/datafiles/current/terminfo.src.gz
gunzip terminfo.src.gz
tic -x terminfo.src
```

This will install a terminfo library under your HOME in the directory
`.terminfo`.

# Verifying

Open up a Tmux session, inside Tmux, the output of `echo $TERM` should be
`screen-256color` or `tmux-256color` depending on which term file you choose to
use, **but the output definitely should not be `xterm-256color`**.

If you have set up all these properly, run the following command on the command
line:

```bash
awk 'BEGIN{
    s="/\\/\\/\\/\\/\\"; s=s s s s s s s s;
    for (colnum = 0; colnum<77; colnum++) {
        r = 255-(colnum*255/76);
        g = (colnum*510/76);
        b = (colnum*255/76);
        if (g>255) g = 510-g;
        printf "\033[48;2;%d;%d;%dm", r,g,b;
        printf "\033[38;2;%d;%d;%dm", 255-r,255-g,255-b;
        printf "%s\033[0m", substr(s,colnum+1,1);
    }
    printf "\n";
}'
```

The above script will print a color band. If something is wrong, the color band
will show obvious gradient between different parts (see image below for an
example).

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181023012639.png">
</p>

If everything works well, you should be able to see a smooth color band both
inside and outside Tmux.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181020165808.png">
</p>

Congratulations, you have finally got it right!

# References

+ https://github.com/tmux/tmux/wiki/FAQ#how-do-i-use-rgb-colour
+ https://stackoverflow.com/q/15375992/6064933
+ [Good explanation about what `terminal-overrides` does](https://github.com/neovim/neovim/issues/7353#issuecomment-334279343).
+ https://stackoverflow.com/q/41783367/6064933
+ https://www.reddit.com/r/neovim/comments/825dj7/the_endless_litany_of_tmux_and_nvim_color_problems/
+ [Why you should not set TERM inside shell config file](https://stackoverflow.com/a/40482330/6064933).
+ [You should not set TERM inside shell config file](https://unix.stackexchange.com/q/139082/221410).

[^1]: If the file `.tmux.conf` does not exist under your HOME, just create an empty one and start editing it..
[^2]: You can find more discussion [here](https://github.com/tmux/tmux/issues/1205).
