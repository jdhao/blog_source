---
title: "Nvim Autocompletion with Deoplete"
date: 2019-06-06 16:40:51+0800
tags: []
categories: [Nvim]
---

[Deoplete](https://github.com/Shougo/deoplete.nvim) is a good auto-completion
plugin for Neovim. In this post, I introduce how to set up auto-completion for
Nvim with the help of deoplete.

<!--more-->

Strictly speaking, it is an auto-completion engine. In order to enable
auto-completion for a certain programming language, you need to install the
corresponding source.

# Install Deoplete

To use deoplete, you should make sure that you are using Python3 . You need
to install `pynvim` -- the Python client to Neovim:

```
pip3 install --user pynvim
```

Deoplete is easy to install. You can install deoplete with
[vim-plug](https://github.com/junegunn/vim-plug):

```
Plug 'Shougo/deoplete.nvim', { 'do': ':UpdateRemotePlugins' }
```

Since deoplete is a completion engine, in order to use auto-completion for a
specific language, you need to install the corresponding `source` for it. The
complete list of sources supporting deoplete can be found
[here](https://github.com/Shougo/deoplete.nvim/wiki/Completion-Sources).

Follow the guide of the specific completion source to install it and enable
completion for that language. For example, you can check
[here](https://jdhao.github.io/2018/12/24/centos_nvim_install_use_guide_en/#auto-completion-plugin-deoplete)
on how to set up deoplete and deoplete-jedi for Python auto-completion.

The minimum settings on deoplete's side is to enalbe it at the start of Nvim:

```
let g:deoplete#enable_at_startup = 1
```

# Other settings

## Disable buffer and around source

The `buffer` and `around` source for deoplete is of little use for
auto-completion. I would rather ignore them. To ignore these two sources, use
the following settings:

```
call deoplete#custom#option('ignore_sources', {'_': ['around', 'buffer']})
```

### References
+ <https://github.com/Shougo/deoplete.nvim/issues/545>

## change the max width of the completion window

Sometimes, the completion term has long strings which are cut down due to
limited completion window width. So change the max width allowed for completion
window, use the following settings:

```
" maximum candidate window length
call deoplete#custom#source('_', 'max_menu_width', 80)
```

### References

+ <https://github.com/Shougo/deoplete.nvim/issues/752>
