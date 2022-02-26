---
title: "A Curated List of File Explorers for Nvim"
date: 2022-02-27T00:10:22+08:00
draft: false
tags: []
categories: [Nvim]
---

File explorer is one the most widely-used type of plugins for Vim/Nvim.
Below are my curated list of file explorers for Nvim.
Some of these plugins are specifically written for Nvim and will not work for Vim.

<!--more-->

# [nerdtree](https://github.com/preservim/nerdtree)

+ vim or Neovim: both
+ platform: Windows, Linux, macOS
+ feature: rich feature with a lot of mappings
+ speed: slow for large directory

# [fern.vim](https://github.com/lambdalisue/fern.vim)

+ vim or nvim: both
+ platform: Windows, Linux, macOS
+ feature:  rich feature
+ speed: implemented in vim script, asynchronous operation so maybe fast

# [vim-dirvish](https://github.com/justinmk/vim-dirvish)

+ vim or nvim: both
+ platform: Windows, Linux, macOS
+ feature:  not very rich, concise without many decoration
+ speed:

# [nvim-tree.lua](https://github.com/kyazdani42/nvim-tree.lua)

+ vim or nvim: nvim only
+ platform: macOS, Linux, Windows
+ feature: seems complete
+ speed: quite fast, but for large number of files, there will be some delays.

# [tree.nvim](https://github.com/zgpio/tree.nvim)

+ vim or nvim: nvim
+ platform: Linux, macOS (author claims to support Windows, but no install script available)
+ feature: medium feature
+ speed: haven't tried, written in C++, maybe very fast

# [defx.nvim](https://github.com/Shougo/defx.nvim)

+ vim or nvim: both
+ platform: Linux, macOS and Windows
+ feature: very rich
+ speed: haven't tried, but may be fast due to python implementation.

# [chadTree](https://github.com/ms-jpq/chadtree)

written in Python (requires at least Python 3.8.2)

+ vim or nvim: nvim only
+ platform: Linux, macOS and Windows
+ feature: very rich
+ speed:  asynchronous and fast

# [lir.nvim](https://github.com/tamago324/lir.nvim)

+ vim or nvim: nvim only (written in lua)

# [telescope-file-browser.nvim](https://github.com/nvim-telescope/telescope-file-browser.nvim)

+ vim or nvim: nvim only
+ platform: Linux, macOS and Windows
+ speed: asynchronous and fast (seems to be faster than nvim-tree.lua, I have tested both on a directory with 100000 files)
+ misc: It is an unconventional file explorer. It uses telescope floating window for file management.

# [Neo-tree.nvim](https://github.com/nvim-neo-tree/neo-tree.nvim)

+ vim or nvim: nvim only
+ platform: Linux, macOS and Windows
+ feature: seems complete
+ speed: may be fast, haven't tried.
