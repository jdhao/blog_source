---
title: "Highlight Groups Cleared after Starting Neovim"
date: 2020-09-22T22:30:16+08:00
tags: []
categories: [Nvim]
---

I want to use a custom highlight group for [yank highlighting](https://neovim.io/doc/user/lua.html#lua-highlight).
Initially, I use the predefined highlight group `IncSearch`. I thought it a bit
dim and want something brighter. So I defined the following highlight group:

<!--more-->

```vim
highlight YankColor ctermfg=59 ctermbg=41 guifg=#34495E guibg=#2ECC71
```

Then, I use it in function `vim.highlight.on_yank{}`:

```vim
augroup highlight_yank
 autocmd!
 au TextYankPost * silent! lua vim.highlight.on_yank{higroup="YankColor", timeout=700}
augroup END
```

However, when I started Neovim, the custom yank highlight did not work. The
output of `:hi YankColor` shows:

```txt
YankColor      xxx cleared
```

After digging up a bit, I find that it is because [the colorscheme I use](https://github.com/lifepillar/vim-gruvbox8)
(a variant of gruvbox) will use `:hi clear` command to clear all custom
highlight and reset highlighting to the defaults (see [here](https://github.com/lifepillar/vim-gruvbox8/blob/master/colors/gruvbox8.vim#L11) for example).

So my custom highlight does not work anymore. A solution is to use a special
event [`ColorScheme`](https://neovim.io/doc/user/autocmd.html#ColorScheme),
which is triggered whenever we change the colorscheme. After changing the color
scheme, we redefine the custom highlight group:

```vim
augroup custom_highlight
  autocmd!
  au ColorScheme * highlight YankColor ctermfg=59 ctermbg=41 guifg=#34495E guibg=#2ECC71
augroup END
```

After using this autocmd, everything works as expected.

Ref:

+ [The right way to override any highlighting](https://gist.github.com/romainl/379904f91fa40533175dfaec4c833f2f)
+ [Why do custom highlights in my vimrc get cleared or reset to default?](https://vi.stackexchange.com/q/3355/15292)
+ https://superuser.com/q/466662/736190
