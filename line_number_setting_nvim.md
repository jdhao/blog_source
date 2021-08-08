---
title: "Line Number Settings for More Efficient Movement in Neovim"
date: 2019-01-11 20:25:31 +0800
tags: [Windows, CentOS, Tmux, Mac]
categories: [Nvim]
---

In this post, I would like to share line number settings in Neovim to move the
cursor more efficiently.

<!--more-->

# Absolute number and relative number in Neovim

## Show absolute line number

To know where we are in a file, it is useful to show the line number on the
leftmost column of the window. To show line numbers in neovim, we can set the
`number` option:

```vim
set number
```

The absolute line number will be shown at the leftmost column of current
window.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200613021435.png" width="600">
</p>

## Combine absolute number and relative number

While the `number` option is useful, it is not convenient for us to move the
cursor to other lines. Because cursor movement in Neovim is based on relative
distance. For example, to go to 3 lines above or below the current line, we use
`3k` or `3j`. If we want to go to a specific line, we have to manually
calculate the distance between current cursor line and the destination line,
which is cumbersome and error-prone.

To alleviate this issue, Neovim also support showing relative number:

```vim
set relativenumber
```

This option can be combined with the `number` option. The end result is that
only the cursor line shows absolute line number, and all the other lines show
relative number( or distance) relative to current line.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200613022102.jpg" width="600">
</p>

# Automatic toggle of relative line number

In some situations, it is more convenient to see absolute line numbers, e.g.,
when we want to debug a file and run to specific lines. We can the use plugin
[vim-numbertoggle](https://github.com/jeffkreeftmeijer/vim-numbertoggle) to
automatically toggle relative number based on several events. e.g., when we
enter insert mode or lost focus of a window.

## Automatic number toggle does not work inside Tmux?

I have found that the number toggle function does not work inside tmux sessions
by default[^1]. If I open two tmux panes side by side and open a file in
Neovim in one pane and then switch to another tmux pane, the relative line
number in Neovim does not change to absolute number.

I opened an issue on the Neovim GitHub repo and get the right answer from the
developer. We need to turn on the `focus-events` option for tmux. From the Tmux
documentaion:

```
focus-events [on | off]
        When enabled, focus events are requested from the terminal if
        supported and passed through to applications running in tmux. Attached
        clients should be detached and attached again after changing this
        option.
```

We need to edit the tmux config file `~/.tmux.conf` and add the following
setting:

```
set -g focus-events on
```

Refresh the tmux session or start a new tmux session and automatic relative
number toggle should work as expected now.

# References

+ Nvim documentation: `:h number_relativenumber`.
+ [Automatic relative number toggle](https://jeffkreeftmeijer.com/vim-number/).
+ [Issue opened in Neovim GitHub repo](https://github.com/neovim/neovim/issues/9486).
+ [Issue opened in vim-numbertoggle repo](https://github.com/jeffkreeftmeijer/vim-numbertoggle/issues/45).
+ [Questions on StackExchange](https://vi.stackexchange.com/q/18515/15292).

[^1]: I am using Tmux 2.8-rc
