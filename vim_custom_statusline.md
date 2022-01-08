---
title: "Building A Vim Statusline from Scratch"
date: 2019-11-03 14:22:20+0800
tags: [Vim]
categories: [Nvim]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20191103144006.png">
</p>

I have been using [vim-airline](https://github.com/vim-airline/vim-airline) to
customize my statusline ever since I started using Neovim. It works great.
Recently, I started [a repo](https://github.com/jdhao/minimal_vim) named
`minimal_vim` to create a minimal configuration without external plugins for
both Vim and Neovim. During the process, I learned how to build a custom
statusline from scratch.

<!--more-->

The primary source of my reference is the Vim help on `statusline` (see `: h
'statusline'`). It is well written and should give you enough knowledge on
building a simple statusline.

The Vim statusline consists of a number of items with special format to
indicate what we want to show and how they should be styled, e.g., width, color,
alignment, padding etc.

# Statusline item format

Usually, the statusline consists of multiple printf style `%` items for showing
various info about current file, e.g., `%F` is used to show the full path of
current file. The complete format for items are as follows:

```
%-0{minWidth}.{maxWidth}{item}
```

+ `-` means to align the item to the left instead of right (the default).
+ `0` is the leading zeros for items that return numeric numbers and is overridden by `-`.
+ `minWidth` and `maxWidth` decide the min and max length of the item to be
shown.

All fields are optional except `{item}` itself.

# Good conventions: build statusline bit by bit

Since we will use several items to build our statusline, the best practice is
to concatenate the various components to build a statusline (also see `:h
set+=`) instead of using a single long strings to set up the statusline.
Building the statusline incrementally make it easier to understand and edit.

For example, we use the following

```vim
set statusline=
" show full file path
set statusline+=%F
" show current line number
set statusline+=%l
```

# Show the result of functions or an expression

Vim provides some default items to show info about current file, but that may
not be enough. We may want to show the result of a function or show strings
based on some conditions. Vim use `%{}` to evaluate functions or expressions
and show the returned strings in statusline.

To show the current mode in statusline, we can use the following setting:

```vim
let g:currentmode={
       \ 'n'  : 'NORMAL ',
       \ 'v'  : 'VISUAL ',
       \ 'V'  : 'V·Line ',
       \ "\<C-V>" : 'V·Block ',
       \ 'i'  : 'INSERT ',
       \ 'R'  : 'R ',
       \ 'Rv' : 'V·Replace ',
       \ 'c'  : 'Command ',
       \}

set statusline=
set statusline+=\ %{toupper(g:currentmode[mode()])}
```

Based on which mode we are in, the statusline will change automatically.

To show a modified sign `[+]` based on whether current file has been modified,
we can use the following setting:

```
set statusline+=%{&modified?'[+]':''}
```

# Using group

Group is used to set width and alignment for several elements as a whole. We
start a group with `(` and end it with `%)`, i.e., `(group_element%)`. For
example, to show file format, we may use the following setting:

```
set statusline+=%-7([%{&fileformat}]%)
```

The above group has min width 7 and is left-aligned.

# Using highlight groups

Ever wondering how to show different colors for different parts of the
statusline? It is simple: use highlight groups.

There are two slightly different ways to use highlight.

## Use a highlight group name

The first way is to use the name of a highlight group. We can check what
highlight groups are available using `:highlight` command. For example, to use
highlight group `Warnings`, we can use the following setting:

```vim
set statusline+=%#Warnings#
set statusline+=%{&bomb?'[BOM]':''}
```

The highlight group will take effect from the point it is used in statusline.
To switch a highlight, we just need to provide another highlight group at the
desired position (which means that if you do not switch it, any later element
in statusline use that highlight group).

## Use special user defined highlight groups

Vim also allows us to define 9 special highlight groups named from `User1` to
`User9` and refer to them in statusline with a special syntax like the
following:

```vim
" use highlight group User2
set statusline+=%2*
```

First, we need to define the needed user highlight group:

```vim
hi User1 ctermfg=0 ctermbg=114
hi User2 ctermfg=114 ctermbg=0
```

Then we can use them in statusline settings. To return to the default
Statusline colors, we can use:

```vim
set statusline+=%*
```

# Common caveat to avoid

The most common error is failing to escape some special characters. Since space
and `|` have special meanings (see also `:h option-backslash`), we need to use
backslash to escape them to include a literal one. For example:

```
set statusline+=%{&filetype!=#''?&filetype.'\ ':'none\ '}
```

The above setting will show the current filetype followed by a space or `none`
followed by space if filetype is empty.

`%` is also a special character. To include a literal `%` char, we need to use
`%%`. For example, to show the percentage of current cursor position in the
buffer, use:

```
set statusline+=%2p%%
```

# References

+ https://gist.github.com/suderman/1243665
+ https://gist.github.com/autrimpo/f40e4eda233977dd3a619c6083d9bebd
+ [Building your own statusline.](https://shapeshed.com/vim-statuslines/)
+ [Scrooloose's vimrc.](https://github.com/scrooloose/vimfiles/blob/master/vimrc#L161)
+ [A better vim statusline?](https://stackoverflow.com/q/5375240/6064933)
+ http://got-ravings.blogspot.com/search/label/statuslines
