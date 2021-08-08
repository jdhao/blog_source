---
title: "Introducing better-esacape.vim: My First Vim/Neovim Plugin"
date: 2020-12-18T23:22:20+08:00
draft: false
tags: []
categories: [Nvim]
---

After using and learning Neovim for over 2 years, I have finally published my
first plugin -- better-escape.vim.

<!--more-->

[Better-escape.vim](https://github.com/jdhao/better-escape.vim) is a plugin to help users escape from insert mode quickly
without experiencing the lagging that comes with Vim mappings.

As a Vim user, a very popular mapping to escape insert mode is to map [`jj`](https://github.com/search?q=inoremap+jj&type=code)
or [`jk`](https://github.com/search?q=inoremap+jk&type=code) or [`kj`](https://github.com/search?q=inoremap+kj&type=code) etc. to <kbd>Esc</kbd>,
since the Esc key is hard to reach without moving your hand from the home row.

```vim
inoremap jk <ESC>
inoremap jj <ESC>
inoremap kj <ESC>
```

However, this comes with an annoyance. If you use `jk` to escape insert mode,
whenever you press `j` in insert mode, Vim will not write it to the buffer, it
will wait for [`timeoutlen`](http://vimdoc.sourceforge.net/htmldoc/options.html#'timeoutlen')
milliseconds to see whether you want to use the `jk` mapping or you want to
input `j` literally. This issue is easily noticeable when we have a relatively large
`timeoutlen` value.

In my [previous post](https://jdhao.github.io/2020/11/23/neovim_better_mapping_for_leaving_insert_mode/), I
have already talked about this topic and provided a solution. The solution is to
use an insert mode mapping for `k` only. When we press `k`, we check if the previous
character is `j`. If that is true, we will erase previous `j` and go to normal mode.
Otherwise, `k` is inserted as is. There is no lagging any more. The problem is that
now we can not input `jk` directly in insert mode. To insert `k` when the previous
character is `j`, we need to press `Ctrl-V`, then press `j`.

In better-escape.vim, I have a smarter solution. We will check the time spent
between pressing `j` and `k` in insert mode. If the time is above a predefined
threshold, `jk` will be inserted literally. Otherwise, we will escape from
insert mode. With this plugin, you can easily customize the shortcut for
escaping insert mode, as well as the time threshold used.
