---
title: "Fast Movement and Navigation Inside Vim or Neovim"
date: 2019-06-26T22:51:43+08:00
tags: []
categories: [Nvim]
---

# Introduction #

One key spirit of Vim is to accomplish something in as fewer key strokes as
possible. When we repeat a command several times to perform an action, we need
to ask ourselves if there is a better way to do it. Only in this way, can we
make progress in mastering Vim. For example, to delete five lines of texts, if
we press `dd` five times, we are certainly doing it the wrong way. After
looking up the Vim manual, we know that we can use `5dd` or `d4j`: only three
keystrokes are needed instead of ten!

<!--more-->

The other day, I saw a post in a forum asking for help on how to quickly move
the cursor inside a buffer. After using Neovim for nearly a year, I would like
to share what I have learnt about fast movement and navigation inside
Neovim/Vim.

There are hundreds or thousands of articles about basic Vim command to move the
cursor. You can check [this cheatsheet](https://vim.rtorr.com/) to quickly get
an overview.

Here I want to to talk something more *advanced*, at least in my opinion.
Unless otherwise notified, all the commands below are performed in normal mode.

# Quickly go to a line

I have observed that one common and inefficient behaviour of Vim novices is
that they often do not know that most Vim commands can be prefixed with a
number to avoid manual repeat.

In this part, I will talk about how to quickly go to line. Beginners often
press `j` or `k` non-stop in order to navigate to a specific line below or
above the current cursor line. This is a terrible and highly inefficient way to
go to where you want.

To go to a line, first press the line number, then press `gg` or `G`. For
example, to go to line `87`, press either `87gg` or `87G`.

## Relative number ##

We can also move the cursor relatively based on the distance between the
destination line and current line. To go to ten lines below or above, we can
press `10j` or `10k`.

This requires less key strokes once a file reaches hundreds of lines. The only
inconvenience is that we have to manually calculate the distance between the
destination line and cursor line. In order to be more efficient, I strongly
suggest you turning on both the [`number`](https://neovim.io/doc/user/options.html#'number') and
[`relativenumber`](https://neovim.io/doc/user/options.html#'relativenumber') options:

```vim
set number
set relativenumber
```

After setting these two options, the cursor line will show its absolute line
number, while the other lines will show the relative distance to the current
line. No more manual calculation needed!

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190626234333.png" width="600">
</p>

For more settings about line number in Neovim, see
[my other post](https://jdhao.github.io/2019/01/11/line_number_setting_nvim/).

# Precise cursor movement #

Vim provides the [`f`, `t` or `F`, `T` command](https://stackoverflow.com/questions/9402607/how-to-jump-to-a-specific-character-in-vim)
to jump forward or backward to a character in current line, and you can repeat
the action by pressing `,` or `;` (see `:h ,` or `:h ;`). While these
commands are useful, they are not powerful enough to be of real value:

+ The movement is restricted to current line.
+ There are possibly many repetitions since you can only provide one character.
As a result, you may need to press `;` several times to go to where you want.

[vim-sneak](https://github.com/justinmk/vim-sneak)[^1] solves these two issues
neatly. With vim-sneak, you can go anywhere your eyes can see in just four key
strokes in most cases. It is one of the most amazing plugins I have ever seen.

After installing this plugin, add the following minimal settings to your
configuration:

```vim
let g:sneak#label = 1
nmap f <Plug>Sneak_s
nmap F <Plug>Sneak_S
```

The above settings will map `f` and `F` to search forward and backward
respectively and use the sneak label mode.

For example, if you want to go to the character `i` that is followed by `m`,
first press `f`, then press `im`. All occurrences of `im` will be highlighted
and given a label (see image below).

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190627004741.png" width="800">
</p>

Press the character in a label, the cursor will jump to that location, which is
really amazing.

# Quickly go to a previous edit position

When I am writing Python source files, a pattern I notice is that I often
forget to import a package or module when I need to use it. My usual work flow
will be like:

+ Change to normal mode with `ESC`.
+ Go to the top of the source file and add the import statement I need.
+ Make other necessary adjusts.
+ Scroll down the source file and find the line I have been editing previously.
+ Go to the precise location where I am editing.

The annoyance is that it will take some time to go to the exact position we are
editing before. This is when marks come to help[^2]. A mark denotes a
particular position in the buffer. The following is a simple overview to use
marks:

+ `ma`: create a mark named `a` (can be a character in a-z)
+ `` `a ``: go the exact position denoted by mark `a`
+ `'a`: go to the line denoted by mark `a`

With marks, we can simplify our workflow. If we want to move to other places
but want to go back later, we can simply create a mark in this position. After
finishing work in other locations, we can directly go to the previous location
with its mark label.

The native functionality provided by marks are powerful but not very
convenient:

+ If you create several marks, you may not remember the correspondence between
  buffer positions and mark labels.
+ You have to manually select an unused label for newly created marks. It is
  also cumbersome to delete a mark.

I recommend using plugin
[vim-signature](https://github.com/kshenoy/vim-signature) to manage your marks.
This plugin will show the mark labels in the sign column[^3] so that we can
easily go to a marked position. It also provides other commands to manage
marks.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190627222220.png" width="800">
</p>

# Switching buffers quickly

When we are working on a large projects, we may open several files at the
same time. We can go to a buffer quickly if we know its buffer index. To show
the buffer index of opened files, use `:ls`. An example output is:

```
2 %a   "movement_navigation_inside_nvim.md" line 153
5 #    "~/.config/nvim/init.vim"      line 758
```

The number in the first column is the buffer index. To go to a buffer, first
press the buffer index, then press <kbd>Ctrl</kbd> + <kbd>6</kbd>.

It works but are still not convenient enough, because we have to first find
the buffer index using `:ls` command. My solution is to use
[vim-airline](https://github.com/vim-airline/vim-airline), which can be
configured to show the buffer index on the tabline along with the file names.
After installing Vim-airline, add the following settings to your configuration:

```vim
let g:airline#extensions#tabline#enabled = 1
let g:airline#extensions#tabline#buffer_nr_show = 1
```

Restart Nvim and you will find that each buffer has a number on the left of the
file name (see image below).

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190627223843.png" width="800">
</p>

# Conclusion #

In this post, I shared several ways for faster movement and navigation inside
Neovim/Vim. I hope that you can apply the techniques here to improve your
efficiency and save your time. Hopefully, you may share your own ideas on how
to further improve efficiency in the comment below.

[^1]: There is another similar plugin called [vim-easymotion](https://github.com/easymotion/vim-easymotion).
[^2]: see `:h mark-motions` for the documentation on marks.
[^3]: See `:h signs` for the documentation on signs.
