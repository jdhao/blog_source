---
title: "Replace Deoplete-jedi with LSP Finally"
date: 2020-11-04T00:10:03+08:00
draft: false
tags: [LSP, pyls]
categories: [Nvim]
---

<font color="red">Update 2021-07-15: Note that pyls has been deprecated. There
is new fork of it called [pylsp](https://github.com/python-lsp/python-lsp-server), which is maintained by the
community.</font>

After using deoplete along with deoplete-jedi for more than two years, I have
finally decided to try something new for code completion. Yeah, you guess it,
the [Language Server Protocol](https://langserver.org/) (LSP in short).

<!--more-->

LSP is not new. It has been around for several years. Especially with the
[official LSP support in Neovim](https://neovim.io/doc/user/lsp.html) core, I think it is time to go for LSP.

To use LSP, there are two parts involved. First, we need to install a language
server for the language we use. Second, we need to use a plugin that acts as an
LSP client that communicates with this server.

There are quite a few LSP clients out there. Some of the most popular ones are:

+ [coc.nvim](https://github.com/neoclide/coc.nvim)
+ [vim-lsp](https://github.com/prabirshrestha/vim-lsp)
+ [LanguageClient-neovim](https://github.com/autozimu/LanguageClient-neovim)
+ [vim-lsc](https://github.com/natebosch/vim-lsc)
+ Neovim built-in LSP client (you need to install [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig) for easier configuration)

I want something simple to set up, powerful, and yet not too monolithic. So I
choose vim-lsp for a try. Here is how to configure it for Python
auto-completion and more.

First, we need to install a language server for Python.
[pyls](https://github.com/palantir/python-language-server) is good choice. First, install pyls:

```bash
pip install "python-language-server[all]"
```

After that, we need to install several plugins and configure it a bit:

```vim
Plug 'prabirshrestha/vim-lsp'
Plug 'Shougo/deoplete.nvim'
Plug 'lighttiger2505/deoplete-vim-lsp'

" settings for pyls
if executable('pyls')
    " pip install python-language-server
    au User lsp_setup call lsp#register_server({
        \ 'name': 'pyls',
        \ 'cmd': {server_info->['pyls']},
        \ 'allowlist': ['python'],
        \ })
endif

function! s:on_lsp_buffer_enabled() abort
    " use omnifunc if you are fine with it.
    " setlocal omnifunc=lsp#complete
    if exists('+tagfunc') | setlocal tagfunc=lsp#tagfunc | endif
    " some mappings to use, tweak as you wish.
    nmap <buffer> gd <plug>(lsp-definition)
    nmap <buffer> gr <plug>(lsp-references)
    nmap <buffer> gi <plug>(lsp-implementation)
    nmap <buffer> gt <plug>(lsp-type-definition)
    nmap <buffer> <leader>rn <plug>(lsp-rename)
    nmap <buffer> [g <Plug>(lsp-previous-diagnostic)
    nmap <buffer> ]g <Plug>(lsp-next-diagnostic)
    nmap <buffer> K <plug>(lsp-hover)
endfunction

augroup lsp_install
    au!
    " call s:on_lsp_buffer_enabled only for languages that has the server registered.
    autocmd User lsp_buffer_enabled call s:on_lsp_buffer_enabled()
augroup END
```

By default, vim-lsp does not provide auto-completion feature, you need to use
`omnifunc` and trigger completion yourself. If you like this, uncomment the
following line in above config:

```
    " setlocal omnifunc=lsp#complete
```

Then trigger completion via `CTRL-X_CTRL-O` (`Ctrl-X` followed by `Ctrl-O`) in
insert mode.

Plugin `deoplete.nvim` and `lighttiger2505/deoplete-vim-lsp` are used to
provide IDE-like auto-completion feature.

There are other plugins that provide auto-completion support based on vim-lsp, for example,
[`asyncomplete.vim`](https://github.com/prabirshrestha/asyncomplete.vim) along with `asyncomplete-lsp.vim`.

```vim
Plug 'prabirshrestha/asyncomplete.vim'
Plug 'prabirshrestha/asyncomplete-lsp.vim'
```

vim-lsp has a lot of options to tweak, some of the configs are:

```vim
" whether to enable diagnostics for vim-lsp (we may want to use ALE for other
" plugins for that.
let g:lsp_diagnostics_enabled = 1

" show diagnostic signs
let g:lsp_signs_enabled = 1
let g:lsp_signs_error = {'text': '✗'}
let g:lsp_signs_warning = {'text': '!'}
let g:lsp_highlights_enabled = 0

" Do not use virtual text, they are far too obtrusive.
let g:lsp_virtual_text_enabled = 0
" echo a diagnostic message at cursor position
let g:lsp_diagnostics_echo_cursor = 0
" show diagnostic in floating window
let g:lsp_diagnostics_float_cursor = 1
" whether to enable highlight a symbol and its references
let g:lsp_highlight_references_enabled = 1
let g:lsp_preview_max_width = 80
```

See `:h vim-lsp` for more details.

After all these settings, we should get a Python auto-completion, diagnostics
etc. using LSP.
