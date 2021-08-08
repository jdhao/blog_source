---
title: "Firenvim: Neovim inside Your Browser"
date: 2020-01-01T23:51:12+08:00
draft: false
tags: []
categories: [Nvim]
---

<p align="center">
<img
src="https://blog-resource-1257868508.file.myqcloud.com/firenvim.gif">
</p>

# Introduction #

Last year, I have written [a post](https://jdhao.github.io/2019/05/11/nvim_text_input_browser/)
on how to edit texts inside the browser using Vim/Nvim or browser extensions
which have Vim emulations. But none of them is satisfactory enough since they
are either not convenient to use or lacking features compared to real Neovim
empowered by various plugins.

<!--more-->

[Firenvim](https://github.com/glacambre/firenvim) is another project which aims
to solve the problem and has done a great job. Thanks to Neovim's [remote UI
architecture](https://github.com/neovim/neovim/wiki/UI-architecture)[^1],
firenvim can activate a Neovim instance in the background and acts as a remote
UI client for Neovim. Thus, you can use the full power of Neovim and its
plugins inside your browser without any compromises.

# Install #

To use firenvim, first we need to install it as a Neovim plugin using a plugin
manager, for example, with vim-plug:

```
Plug 'glacambre/firenvim', { 'do': { _ -> firenvim#install(0) } }
```

The above setting works for Windows and Linux. For macOS, due to the reason
that `$PATH` variable is changed inside browser, we need to use the following
command on the shell command line:

```bash
nvim --headless -c "call firenvim#install(0, 'export PATH=\"$PATH\"')" -c quit
```

After that, we need to install firenvim extension for
[FireFox](https://addons.mozilla.org/en-US/firefox/addon/firenvim/) or
[Chrome](https://chrome.google.com/webstore/detail/firenvim/egpjdkipkomnmjhjmdamaniclmdlobbo).

Now, close your browser and reopen it. Click in a text area and firenvim should
be able to automatically take over the text area and start a neovim instance.

# Custom settings #

Firenvim provides the variable `g:started_by_firenvim` when it starts
a neovim instance. So you can use this variable to set conditional options
and settings for firenvim. For example, set `laststatus` to zero, and do not
show ruler, since the text area is already very small.

You can also customize the filetype created for each website, as per the doc
[here](https://github.com/glacambre/firenvim#using-different-settings-depending-on-the-pageelement-being-edited).

My custom settings for firenvim are as follows:

```vim
" Disable vim-airline when firenvim starts since vim-airline takes two lines.
if !exists('g:started_by_firenvim')
    Plug 'vim-airline/vim-airline'
    Plug 'vim-airline/vim-airline-themes'
endif

if exists('g:started_by_firenvim') && g:started_by_firenvim
    " general options
    set laststatus=0 nonumber noruler noshowcmd

    augroup firenvim
        autocmd!
        autocmd BufEnter *.txt setlocal filetype=markdown.pandoc
    augroup END
endif
```

For more configuration, see the [doc on project page](https://github.com/glacambre/firenvim#configuring-firenvim).

# References #

+ https://www.reddit.com/r/neovim/comments/bmguyj/embed_neovim_in_your_browser/
+ https://www.reddit.com/r/neovim/comments/d9gk5y/firenvim_has_been_released_on_the_chrome_web_store/
+ Firenvim does not work properly on macOS?
    - https://github.com/glacambre/firenvim/issues/122
    - [Trouble shooting on macOS.](https://github.com/glacambre/firenvim/blob/master/TROUBLESHOOTING.md#make-sure-firenvims-path-is-the-same-as-neovims)


[^1]: Due to this, it only works with Neovim, not vanilla Vim.
