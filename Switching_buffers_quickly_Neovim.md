---
title: "How to Switch between Buffers Quickly in Neovim/Vim"
date: 2018-09-29 16:41:00 +0800
tags: [Vim]
categories: [Nvim]
---

When working on real projects using Neovim, we often open several files in a
window. Each file corresponds to a buffer. How to switch between these buffers
quickly?

<!--more-->

To show a list of opened buffers, we can use the `:buffers` command. Below is
an example output:

```txt
3      "train.py"                     line 1
4 %a   "main.py"                      line 17
6      "keys.py"                      line 1
7      "utils.py"                     line 1
8 #    "val_CTC.py"                   line 2
```

The first number is a unique buffer number corresponding to each buffer.

# The native way

In command mode, some of the commands to change buffers are list below:

+ `:bfirst`: change to first buffer in the buffer list
+ `:blast`: change to last buffer in the buffer list
+ `:bnext`: change to next buffer
+ `:bprevious`: change to previous buffer

Or you can use `:b <TAB>` to choose from currently opened buffers.

If there are a few buffers, it is easy to switch using the above command.
However, if we have a dozen of opened buffers, it is cumbersome to use these
commands.

# Switching buffers using buffer number

We can switch to a buffer quickly if we know its number. If we know the number
of a buffer, we can use `NUM Ctrl-6` to go to a particular buffer (replace
`NUM` with actual buffer number). For example, to switch to `main.py`, we will
press `4<C-6>`.

This is still inconvenient --- since we have to find the buffer number first.

## A better solution for buffer switching

If you have installed [vim-airline](https://github.com/vim-airline/vim-airline)
，you can configure it to show the buffer number on the top of the window
tabline. Just add the following settings to Nvim config file `init.vim`:

```vim
let g:airline#extensions#tabline#enabled = 1
let g:airline#extensions#tabline#buffer_nr_show = 1
```

Restart Nvim and you will find that each buffer has a number on the left of the
file name. Then you can easily go to a buffer.

If you do not want to use vim-airline, there are other plugins to show a buffer
tabline, such as [buf-tabline](https://github.com/ap/vim-buftabline), which is
more lightweight.

# References

+ [Fastest way to switch to a buffer in vim?](https://vi.stackexchange.com/q/2129/15292)
+ <https://github.com/vim-airline/vim-airline/issues/1149>
+ [Nvim documentation](https://neovim.io/doc/user/editing.html#CTRL-^)
