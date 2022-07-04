---
title: "Nvim 0.7.2 Released"
date: 2022-06-29T23:21:58+08:00
markup: markdown
tags: []
categories: [Nvim]
---

Recently the [nvim 0.7.2 version](https://github.com/neovim/neovim/commit/e8ee6733926db83ef216497a1d660a173184ff39) is released following the [0.7.0 release](https://github.com/neovim/neovim/releases/tag/v0.7.0) two months ago.
Here are some of the updates in 0.7.0.

<!--more-->

# Python 2 support is removed

Python 2 support is dropped since nvim 0.7.0.
So command `:py` and `:py3` works the same now.

# Mappings

+ there is a new `vim.keymap.set()` interface, where you can set mapping for multiple modes at the same time.
+ support `desc` field for mappings, which is used to describe what the mapping does. Check [this commit](https://github.com/neovim/neovim/commit/b411f436d3e2e8a902dbf879d00fc5ed0fc436d3).

# Easier inspection of lua table

+ we can use `:lua =expr`: quickly print the value of an expression, e.g., `:lua =vim` will print the `vim` table.
+ The function `vim.pretty_print()` can print lua tables nicely. Use `vim.pretty_print()` to show the content of a lua object

# Autocmd in lua

You can now use native lua function to create autocmd in Lua, no more `vim.cmd()` hack.
See `:h nvim_create_automcmd` for the details.

# Create command in lua

You can now create command in lua via function `nvim_create_user_command()`.

# Filetype.lua

The old way of detecting filetypes from vim is slow. So the new `filetype.lua` is introduced to reduce the time.
TL;DR, add the following config to `init.vim`

```vim
" use filetype.lua instead of filetype.vim
let g:do_filetype_lua = 1
let g:did_load_filetypes = 0
```

or use

```lua
vim.g.do_filetype_lua = 1
vimg.did_load_filetypes = 0
```

if you use `init.lua`.

# Global statusline

Want to use a single statusline for all the windows? Now it is possible, with the following setting:

```vim
set laststatus=3
```

# References

+ https://neovim.io/news/2022/04
+ https://gpanders.com/blog/whats-new-in-neovim-0-7/
+ Hacker News discussion: https://news.ycombinator.com/item?id=31039569
