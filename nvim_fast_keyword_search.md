---
title: "The Fastest Way to Search A Keyword across the Project in Neovim"
date: 2018-10-07T17:34:01+08:00
tags: [search]
categories: [Nvim]
---

When we want to find out where a method is used across the whole project, how
do we do that in Nvim? Various tools are available, for example, [ack](https://beyondgrep.com/documentation/),
[ag](https://github.com/ggreer/the_silver_searcher) or [rg](https://github.com/BurntSushi/ripgrep).

These are the 3 most popular tools to use. In the following post, I will
introduce how to install and use these packages in Neovim (Nvim).

<!--more-->

# Installing the searching tools

## Install ack

Suppose that we want to install it to `$HOME/tools/bin`, you can use the
following install command:

```bash
# directory ~/tools/bin must exist
curl https://beyondgrep.com/ack-2.24-single-file > ~tools/bin/ack

# make it executable
chmod u+x ~/tools/bin/ack
```

Add ack to `PATH` variable in `.bash_profile`:

```bash
export PATH=$HOME/tools/bin:$PATH
```

Then source `.bash_profile` to make the change take effect:

```bash
source ~/.bash_profile`.
```

## Install ag -- the silver searcher

I have found an exceptional tool for this task. It is called [The Silver
Searcher](https://github.com/ggreer/the_silver_searcher), AKA, ag.

Please follow the official guide on [how to install this
tool](https://github.com/ggreer/the_silver_searcher#installing).


## Install rg -- ripgrep

[Ripgrep](https://github.com/BurntSushi/ripgrep) is also a great tool for
searching patterns. It seems that [rg is faster than both ack and ag](https://github.com/BurntSushi/ripgrep#quick-examples-comparing-tools).
So it is also worth a trial.

Follow the [offical guide](https://github.com/BurntSushi/ripgrep#installation) on installing this tools.

# Use these search tools inside Nvim

You can use these tools directly on the command line. If you want to use it
with Vim, you also need to use a *front end*[^1] for it.

## Install ack.vim

To use ack inside Nvim, we also need to install [`ack.vim`](https://github.com/mileszs/ack.vim):

```vimrc
# using vim-plug to install this package
Plug 'mileszs/ack.vim'
```

By default, ack.vim use ack to search for patterns. If you have installed both
ag and rg. You can tell ack.vim to use them instead, with a preference for rg.

```vimrc
" Prefer rg > ag > ack
" https://hackercodex.com/guide/vim-search-find-in-project/
if executable('rg')
    let g:ackprg = 'rg -S --no-heading --vimgrep'
elseif executable('ag')
    let g:ackprg = 'ag --vimgrep'
endif
```

In Nvim normal mode, use `:Ack {PATTERN} [{PATH}]` to search `{PATTERN}`
recursively under directory `{PATH}`. If you omit the `{PATH}` parameter, the
default path is the current path.

Ack.vim will open the first file that matches. If you do not want this
behaviour, you can use `:Ack!` instead. Or, [use the following setting in your
Nvim config](https://github.com/mileszs/ack.vim#i-dont-want-to-jump-to-the-first-result-automatically):

```
cnoreabbrev Ack Ack!
nnoremap <Leader>a :Ack!<Space>
```

It adds a custom shortcut for the `:Ack!` command. For more information, see
the ack.vim documentation.

## If you are a minimalist

There is high chance that you may have installed [fzf](https://github.com/junegunn/fzf)
and [fzf.vim](https://github.com/junegunn/fzf.vim). Then You do not need to install
ack.vim. fzf.vim has builtin support for ag and rg.

Both fzf and fzf.vim is easy to install. After installation, you can use `:Ag
PATTERN` or `rg PATTERN` to search for keyword `PATTERN` under your current
root directory. The search speed is quite fast. Enjoy~

## Another great tool

Recently, I have switched from fzf to
[Leaderf](https://github.com/Yggdroot/LeaderF), which is also a great fuzzy
find tool. To use ripgrep with leaderf, you can use the following command:

```
:Leaderf rg PATTERN
```


**Update history:**

+ 2020-02-21: Add section on Leaderf.

# References

+ Ack install: https://beyondgrep.com/install/
+ https://stackoverflow.com/questions/38398787/how-do-i-search-in-all-files-of-my-project-using-vim

[^1]: By front end, I mean that some Nvim plugin which will invoke this command for you.
