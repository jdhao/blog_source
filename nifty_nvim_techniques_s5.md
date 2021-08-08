---
title: "Nifty Nvim Techniques That Make My Life Easier -- Series 5"
date: 2019-11-11 22:55:02+0800
tags: []
categories: [Nvim]
---

This is the series 5 of my blog posts about some nifty techniques of using
Nvim, which continues my [previous series here](https://jdhao.github.io/2019/09/17/nifty_nvim_techniques_s4/).

<!--more-->

# How do I source other files in the same directory as my config?

As my config becomes longer and longer, I decided to split it into several Vim
scripts. I can then source those files in my `init.vim`. Initially, I wrote
something like `source xxxx.vim`. To my surprise, Nvim complained that it could
not find those Vim script.  After searching on the internet, I find that
`source` command works relatively to your current work directory. In order to
source certain Vim script in the same directory as `init.vim`, we have to use
their absolute paths. Here is what I come up with:

```vim
let g:nvim_config_root = stdpath('config')
let g:config_file_list = ['variables.vim',
\ 'options.vim',
\ 'autocommands.vim',
\ 'mappings.vim',
\ 'plugins.vim',
\ 'ui.vim'
\ ]

for f in g:config_file_list
    execute 'source ' . g:nvim_config_root . '/' . f
endfor
```

In the above settings, we use the
[stdpath()](https://neovim.io/doc/user/eval.html#stdpath%28%29) function to get
the directory of nvim config directory, which is rather convenient and works
across platforms. Then we loop the files we want to source and source them one
by one.

## References

+ [Source all vim files in a directory.](https://stackoverflow.com/q/4500748/6064933)
+ [How to source relative to a file?](https://stackoverflow.com/q/46964475/6064933)

# How to search a pattern only in a line range

Sometimes, we only want to search in a range of lines, i.e., search from line n
to line m. The syntax can be best explained by giving a concrete example.
Suppose we want to search for the word `search` from line 10 to line 20, we can
use the following search pattern:

```vim
/\%>10l\%<20lsearch
```

The start line is given by `\%>10l` and the end line is given by `\%<20l`. For
more info, see `:h search-range` and `:h /\%l`.

## References

+ [Search in a range](https://stackoverflow.com/a/3264176/6064933).

# Add highlight to a pattern

To highlight some patterns, we can use the `:match` command. For example, in
order to highlight trailing white spaces, we may use the following command:

```vim
:match WarningMsg /\s\+$/
```

The 1st argument to `:match` is a valid highlight group[^1]. In the 2nd
argument, the highlight pattern is inside `//`. For more info, see `:h
match-highlight`.

The problem is that we can only highlight one pattern using this command. This
means that if we want to highlight another pattern with `:match` command, only
one pattern get highlighted. Vim provides additional `:2match` and `:3match`
command to some avail. So in total, you can highlight three different patterns
using this command.

Fortunately, Vim also provides the `matchadd()` function which works similarly
to `:match`, except that there is no limit on the number of patterns to match.
For example, to match both trailing white spaces and leading tab characters,
use the following setting:

```vim
call matchadd('Warnings', '\s\+$')
call matchadd('Warnings', '^\t\+')
```

In the above settings, it is important that you quote the pattern to match with
single quote. If you use double quotes, those backslashes will get translated
so that the regex pattern will not right.

## References

+ [Why does the pattern in matchadd() is not matched?](https://vi.stackexchange.com/a/17184/15292)
+ [Difference between single quote and double quote in Vim.](https://vi.stackexchange.com/q/9706/15292)

# How to get the path of currently sourced file?

When Vim is sourcing a file, you can use `<sfile>` inside function `expand()`
to get the path of the script. To get other info about the sourced file, use
`filename-modifiers` (see `:h filename-modifiers`).

```vim
" absolute path of currently sourced file
echo expand('<sfile>:p')

" directory containing currently sourced file
echo expand('<sfile>:p:h')
```

## References

+ [Get the path to the current vimscript being executed.](https://stackoverflow.com/q/4976776/6064933)

# Turn tabs to spaces in a buffer quickly

I do not like tabs in my source file so I have set up the following settings in
my config:

```vim
set tabstop=4
set softtabstop=4
set expandtabs
set shiftwidth=4
```

This setting works great when I am editing source code. However, for existing
code using tabs, Vim/Nvim will not convert tabs into spaces automatically.
Usually, I just replace tabs with four spaces manually: `:s/\t/    /g`. But it
is tedious to type all this.

There is a quicker way to turn tabs into spaces using the `:retab` command. It
will turn tabs into 4 spaces if you use the above settings.

## References

+ [Replace tabs with spaces in vim](https://stackoverflow.com/a/427004/6064933)

[^1]:  You can show all the available highlight groups with `:highlight` command.
