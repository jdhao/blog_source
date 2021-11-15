---
title: "Highlight Yanked Region in Nvim/Vim"
date: 2020-05-22 20:48:26+0800
tags: [Vim]
categories: [Nvim]
---

**update (2020-09-19): update the settings for Nvim to reflect the latest
changes in API.** 

In Vim or Neovim, showing the yanked region in color gives use visual feedback
on what we have copied. Here is how you can highlight yanked region in Vim or
Neovim.

<!--more-->

# For Neovim and vim

There is a plugin
[vim-highlightedyank](https://github.com/machakann/vim-highlightedyank) for
this, which works both for vim and Neovim. Install it with your favorite plugin
manager and it just works. You can also adjust highlight duration by adding the
following config:

```vim
" set highlight to 1000 ms
let g:highlightedyank_highlight_duration = 1000
```

# Neovim only

If you are using the [latest
version](https://github.com/neovim/neovim/releases/tag/nightly) of Neovim. They
have make this little feature built-in in [this pull
request](https://github.com/neovim/neovim/pull/12279) (see also `:h
lua-highlight`).

Install the latest version of Neovim and add the following config to your
`init.vim`:

```vim
augroup highlight_yank
    autocmd!
    au TextYankPost * silent! lua vim.highlight.on_yank{higroup="IncSearch", timeout=700}
augroup END
```

The first parameter is the highlight group to use, and the second parameter is
the highlight duration time measured in milliseconds.

Enjoy it. One more plugin to remove :)
