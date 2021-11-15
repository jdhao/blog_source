---
title: "A Hands-on Experience with Neovim's Built-in LSP Support"
date: 2019-11-20T22:41:16+08:00
tags: [LSP]
categories: [Nvim]
---

<details>
<summary><font size="2" color="red">update log</font></summary>

+ <font color="blue">2021-07-15: use pylsp instead of pyls since pyls is obsolete.</font>
+ <font color="blue">2021-04-08: update post based on latest version of Neovim-lsp.</font>
+ <font color="blue">2020-09-14: update lsp config to reflect the latest changes and add more plugins.</font>

</details>

Just a few days ago, the built-in [Language Server Protocol](https://microsoft.github.io/language-server-protocol/)
support [has been merged](https://github.com/neovim/neovim/pull/11336) into Neovim master.

This post is a hands-on experience of setting up LSP support for Python with
the [python-lsp-server](https://github.com/python-lsp/python-lsp-server).

Note that settings here may be obsolete due to rapid evolving of the built-in lsp.

<!--more-->

# Install Neovim

To try the built-in LSP, we have to install Neovim master build release or build
Neovim from source. The easiest way is to install Neovim [nightly release](https://github.com/neovim/neovim/releases/tag/nightly).

# Install nvim-lspconfig

To ease configuration, the contributor to LSP has created the [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig)
plugin. We can use vim-plug to install this plugin:

```vim
Plug 'neovim/nvim-lspconfig'
```

## Configuration for Python

First, we need to install [python-lsp-server](https://github.com/python-lsp/python-lsp-server). We can install
python-lsp-server and all its dependencies with the following command:

```vim
pip install 'python-lsp-server[all]'
```

Open `init.vim` and add the following settings:

```vim
lua << EOF
require("lspconfig").pylsp.setup{}
EOF

set completeopt-=preview

" use omni completion provided by lsp
autocmd Filetype python setlocal omnifunc=v:lua.vim.lsp.omnifunc
```

Then open a Python source file. Note that the built-in LSP does not support
auto-completion like what is provided by deoplete. To invoke completion, we
need to press `<C-X><C-O>`[^1]. If all goes well, you should be able to see a
completion menu with all the completion items.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200914230930.jpg" width="800">
</p>

## The auto-completion experience ##

Neovim may hang or lag behind when you start completion since all the
compeltion items will be shown in the completion menu and the number of items
may be huge. There is also no auto-completion like what is provided by other
more mature completion engines. It may take a while before you can really use
this for your daily work. Right now, I suggest you stick to your current
completion engines.

# Useful mappings #

There are other functions provided by LSP which you may find useful. You can
add the following mappings for those functions:

```vim
nnoremap <silent> gd    <cmd>lua vim.lsp.buf.declaration()<CR>
nnoremap <silent> <c-]> <cmd>lua vim.lsp.buf.definition()<CR>
nnoremap <silent> K     <cmd>lua vim.lsp.buf.hover()<CR>
nnoremap <silent> gD    <cmd>lua vim.lsp.buf.implementation()<CR>
nnoremap <silent> <c-k> <cmd>lua vim.lsp.buf.signature_help()<CR>
nnoremap <silent> 1gD   <cmd>lua vim.lsp.buf.type_definition()<CR>
nnoremap <silent> gr    <cmd>lua vim.lsp.buf.references()<CR>
nnoremap <silent> g0    <cmd>lua vim.lsp.buf.document_symbol()<CR>
nnoremap <silent> gW    <cmd>lua vim.lsp.buf.workspace_symbol()<CR>
```

Because some of the methods are not supported by a particular lanuage server.
So not all these mappings will work as expected. You may encounter errors when
you use these mappings if the language server does not implement such features.

# helper plugins

Although we can use the LSP feature with the above minimum configuration. The
user experience is not great, at least worse than Deoplete. There are plugins
made around LSP to make your life easier and more comfortable.

<del>The plugin [completion-nvim](https://github.com/nvim-lua/completion-nvim) and
[diagnostic-nvim](https://github.com/nvim-lua/diagnostic-nvim) are two such
plugins. completion-nvim provides auto-completion for LSP, and diagnostic-nvim
provides better disgnostic experience.</del>

<del>The minimal and complete config is list below:</del>

<details>
<summary><font size="2" color="red">Click to show the code.</font></summary>

```vim
" Note we need to install these plugins using PlugInstall command before using them.
call plug#begin('~/.local/share/nvim/plugged')
Plug 'neovim/nvim-lsp'
Plug 'nvim-lua/completion-nvim'
Plug 'nvim-lua/diagnostic-nvim'
Plug 'lifepillar/vim-gruvbox8'
call plug#end()

lua << EOF
require'nvim_lsp'.pyls.setup{}
EOF

set completeopt-=preview

" use omni completion provided by lsp
autocmd Filetype python setlocal omnifunc=v:lua.vim.lsp.omnifunc

" some shortcuts
nnoremap <silent> gd    <cmd>lua vim.lsp.buf.declaration()<CR>
nnoremap <silent> <c-]> <cmd>lua vim.lsp.buf.definition()<CR>
nnoremap <silent> K     <cmd>lua vim.lsp.buf.hover()<CR>
nnoremap <silent> gD    <cmd>lua vim.lsp.buf.implementation()<CR>
nnoremap <silent> <c-k> <cmd>lua vim.lsp.buf.signature_help()<CR>
nnoremap <silent> 1gD   <cmd>lua vim.lsp.buf.type_definition()<CR>
nnoremap <silent> gr    <cmd>lua vim.lsp.buf.references()<CR>
nnoremap <silent> g0    <cmd>lua vim.lsp.buf.document_symbol()<CR>
nnoremap <silent> gW    <cmd>lua vim.lsp.buf.workspace_symbol()<CR>

set completeopt=menuone,noinsert,noselect

lua << EOF
local on_attach_vim = function(client)
  require'completion'.on_attach(client)
  require'diagnostic'.on_attach(client)
end
require'nvim_lsp'.pyls.setup{on_attach=on_attach_vim}
EOF

let g:diagnostic_enable_virtual_text = 1
let g:diagnostic_enable_underline = 0
let g:diagnostic_auto_popup_while_jump = 1
let g:diagnostic_insert_delay = 1

colorscheme gruvbox8_hard
```
</details>

A good plugin to install is [nvim-compe](https://github.com/hrsh7th/nvim-compe) which provides auto-completion for the built-in lsp.

# References #

+ https://github.com/neovim/nvim-lspconfig#nvim-lspconfig
+ https://www.reddit.com/r/neovim/comments/dx098o/github_neovimnvimlsp_common_configurations_for/
+ https://www.reddit.com/r/neovim/comments/dvd8sz/builtin_language_server_with_my_preferred_style/

[^1]: For more info, see `:h 'omnifunc'` and `:h i_CTRL-X_CTRL-O`.
