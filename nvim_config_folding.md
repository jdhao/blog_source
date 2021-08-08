---
title: "How to Create Proper Folding for Vim/Nvim Configuration"
date: 2019-08-16 22:56:20+0800
tags: [folding, Vim]
categories: [Nvim]
---

![Folded config](https://blog-resource-1257868508.file.myqcloud.com/20190817223054.png)

<!--more-->

# Introduction #

I started using Neovim about a year ago. During the process, I have been
constantly evolving my configuration (the `init.vim` file): adding some
settings and plugins and dropping others. At its peak, my `init.vim` has over
1600 lines of settings and comments. Managing a configuration of this size
requires grouping the relevant settings into different sections or even
subsections. This is when I started thinking about folding my configuration
properly. The end result is shown in the title image.

Nvim offers several methods to fold a file:

+ [fold-manual](https://neovim.io/doc/user/fold.html#fold-manual)
+ [fold-indent](https://neovim.io/doc/user/fold.html#fold-indent)
+ [fold-expr](https://neovim.io/doc/user/fold.html#fold-expr)
+ [fold-marker](https://neovim.io/doc/user/fold.html#fold-marker)
+ [fold-syntax](https://neovim.io/doc/user/fold.html#fold-syntax)
+ [fold-diff](https://neovim.io/doc/user/fold.html#fold-diff)

I found that `fold-expr` and `fold-marker` is easy to use and understand. In
this article, I only introduce these two methods.

# Folding with Marker #

When we fold with a marker, Nvim will create a fold where the fold marker
appears. The default fold marker is `{{{` (for opening a fold) and `}}}` (for
closing a fold). To fold the Nvim config with marker, you have to use `marker`
as the folding method:

```vim
set foldmethod=marker
```

To indicate the fold level, you can append a number after the fold marker. I
show an example config using fold markers below:

```vim
"{{{ Some settings
"{{{2 Plugin install
call plug#begin(expand('~/.local/share/nvim/plugged'))
Plug 'neoclide/coc.nvim', {'do': './install.sh'}
Plug 'Shougo/neco-vim'
Plug 'neoclide/coc-neco'
call plug#end()
"}}}2

"{{{2 Builtin options
set number
set relativenumber
set pyxversion=3
set foldmethod=marker
set wildoptions=pum
"}}}2
"}}}1

"{{{ Color schemes
set background=dark
color desert
"}}}
```

In the above sample configuration, I define two level 1 sections. Inside the
first level 1 section, there are two level 2 sub-sections.

If you open Nvim with the above configuration:

```bash
nvim -u init.vim init.vim
```

the folding is like the following:

```
+-- 17 lines: Some settings ··········································

+--  4 lines: Color schemes···········································
```

Two level 1 folds will be shown. Go to the first level 1 fold and use `zo` to
open the fold. Now the buffer will be shown as the following:

```
"{{{ Some settings
+---  7 lines: Plugin install·········································

+---  7 lines: Builtin options········································
"}}}1

+--  4 lines: Color schemes···········································
```

In the closed fold line, there are some texts shown to indicate the meaning of
this fold and how many lines have been folded[^1]. It is called
[`foldtext`](https://neovim.io/doc/user/options.html#'foldtext') in Nvim. By
default, the `foldtext` option will use the function
[`foldtext()`](https://neovim.io/doc/user/eval.html#foldtext()) to evaluate the
opening fold line and return a reasonable string as the fold text. See the
`foldtext()` doc for details. If you are not satisfied with the style of the
default fold text, you may define your fold text function to replace the
`foldtext()`. We will cover that later in this post.

# Folding with Expression #

Folding with marker is not flexible enough since you have to use the marker and
indicate the folding level by appending a number behind the marker. Folding
with expression is more flexible and powerful.

To fold Nvim config with an expression, you have to use `expr` for the
`foldmethod` option and define your custom folding function:

```vim
set foldmethod=expr
set foldexpr=VimFolds(v:lnum)
```

In the above settings, we define the folding function `VimFolds()`, which will
evaluate each line of the config and return its folding level. The variable
`v:lnum` is a special variable which indicates the current line the folding
function is evaluating. That is where the power of `fold-expr` resides: you can
structure your configuration the way you want and use the folding function to
parse the config and returns the correct folding level for each line.

With custom folding expression, we can define our own fold indicator. For
example, I am currently using the number of `{` to indicate the level of a
folding:

+ `"{`: level 1
+ `"{{`: level 2
+ `"{{{`: level 3
+ ......


I use the following folding function to parse the configuration:

```vim
function! VimFolds(lnum)
    " get content of current line and the line below
    let l:cur_line = getline(a:lnum)
    let l:next_line = getline(a:lnum+1)

    if l:cur_line =~# '^"{'
        return '>' . (matchend(l:cur_line, '"{*') - 1)
    else
        if l:cur_line ==# '' && (matchend(l:next_line, '"{*') - 1) == 1
            return 0
        else
            return '='
        endif
    endif
endfunction
```

For lines starting with `"{`, the above function will extract the number of
curly braces and return its folding level. For empty lines which are followed
by a level 1 fold, its folding level is 0, which means that this line is not
in a fold and will not be folded. For other lines, its folding level is the
same as the previous line.

The format for the folding level can be found in `:h fold-expr`.

# Custom Folding Text #

Now that we can define the folding level by the folding expression. We need to
customize the folding text as well. Or the fold text produced by the default
`foldtext()` function will look ugly:

```
+-- 21 lines: { Some settings··················································

+-- 27 lines: { Custom functions···············································

+--  4 lines: { Color schemes··················································
```

To customize the fold text, as I have mentioned earlier, we need to create a
custom fold text function to produce the folding text we want.

Below I show a custom folding text function:

```vim
function! MyFoldText()
    let line = getline(v:foldstart)
    let folded_line_num = v:foldend - v:foldstart
    let line_text = substitute(line, '^"{\+', '', 'g')
    let fillcharcount = &textwidth - len(line_text) - len(folded_line_num)
    return '+'. repeat('-', 4) . line_text . repeat('.', fillcharcount) . ' (' . folded_line_num . ' L)'
endfunction
```

In the above function, `v:foldstart` and `v:foldend` are the special variable
provided by Vim for a particular folding level. We need to set the `foldtext`
option to use the custom function:

```vim
set foldtext=MyFoldText()
```

Reopen Nvim and now the fold text becomes:

```
Some settings  -------------------------------------------------------- (20 L)···········

Custom functions  ----------------------------------------------------- (26 L)···········

Color schemes  --------------------------------------------------------- (3 L)···········
```

The trailing `·` characters at the end of each folding line is added by Nvim if
there are spaces after the fold text in the current window. If you do not want
these trailing characters, you can add the following setting to Nvim config:

```vim
" note that there is a space character behind backslash
set fillchars=fold:\
```

# Conclusion #

In this post, I introduced how to create folding for the Nvim configuration
file and use custom folding text for a fold. With this knowledge, you can
create fancy foldings and folding text as you want.

# References #

+ https://vi.stackexchange.com/questions/3814/is-there-a-best-practice-to-fold-a-vimrc-file

[^1]: See `:h fold-foldtext` for detailed explanations on fold text.
