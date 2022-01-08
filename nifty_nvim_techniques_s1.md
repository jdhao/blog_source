---
title: "Nifty Little Nvim Techniques to Make My Life Easier -- Series 1"
date: 2019-03-28 00:24:33+0800
tags: [Vim, text-object]
categories: [Nvim]
---

In this post, I want to share some nifty techniques that make my editing
experience in [Neovim](https://github.com/neovim/neovim) smoother.

<!--more-->

# How do we select the current line, but not including the newline character?

In Neovim, if we want to select from the current cursor position to the end of
the line, we usually use `v$`. However, the annoyance is that newline character
is also selected. We have to press `h` to move the cursor one character left.

How do we select only to the last character of the line? Fortunately, we can
use `g_` to do that. According to the documentation:

```txt
g_          To the last non-blank character of the line and
            [count - 1] lines downward |inclusive|.
```

To select from the cursor position to the last character of this line, we can
use `vg_`.

There is also a [`selection`](https://neovim.io/doc/user/options.html#'selection')
option in Neovim that controls how the selection
behaves. If we use `set selection=exclusive`, then the last character will not
be included when we use `$`.

Another way is to remap `$` in visual mode. Add the following settings to your config:

```vim
xnoremap $ g_
```

References

+ [Extend visual selection til the last character on the line (excluding the new line character)](https://vi.stackexchange.com/q/12607/15292)
+ [Select entire line in VIM, without the new line character](https://stackoverflow.com/q/20165596/6064933)

# Case-changing operators

Neovim provides several shortcuts to change character cases quickly. All the
following operations should be followed by motions (e.g., `$`, `w`, `e` etc.)
or [text objects](https://neovim.io/doc/user/motion.html#text-objects):

+ `g~`: switch the case of character
+ `gu`: change characters to lower case
+ `gU`: change characters to upper case

# Add character pair to text objects

Sometimes, we want to add a pair of characters to existing text objects. The
`ys` operator provided by [vim-surround](https://github.com/tpope/vim-surround)
is used for that purpose. To use it, the format is

```
ys<TextOjbect><char>
```

Suppose we have the following texts, and we want to surround the word `welcome`
with a pair of square bracket (`[]`)

```
# | indicate cursor position
"wel|come to a new world"
```

Our key strokes should be `ysiw]`[^1]

The above text now becomes:

```
"[welcome] to a new world"
```

If you want to surround `"welcome to a new world"` with `()`, you can use `ysi")`[^2]

# How do I check the help doc for a keyword under the cursor?

When we are browsing the help file, we may want to check the doc for a keyword
quickly. For example, when the cursor is in the keyword `clipboard` (keywords
in the help files are colored as opposed to the regular text), how do we check
its documentation immediately?

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200430135552.png" width="400">
</p>

It is very simple: just press `K`, and you will go the documentation of the
keyword. You can also press <kbd>Ctrl</kbd>+<kbd>]</kbd> to go to the
documentation.

If you have enabled mouse support in nvim (`set mouse=a`), you can also double
click the keyword to open its documentation. If you are using [mintty](http://mintty.github.io/)
terminal, clicking the keyword while you are pressing <kbd>Ctrl</kbd> will
also work.

By the way, to go back to previous position in the help file, press
<kbd>Ctrl</kbd>+<kbd>O</kbd> or <kbd>Ctrl</kbd>+<kbd>T</kbd>.

References

+ [Nvim user manual](https://neovim.io/doc/user/)
+ [How to navigate in Nvim help file](https://vi.stackexchange.com/q/2136/15292).
+ [Search vim help for keyword under cursor](https://stackoverflow.com/q/15867323/6064933)
+ <https://vimrcfu.com/snippet/19>

# Setting up `textwidth` in Nvim

For text files such as Markdown, LaTeX, we may want to restrict the line length
to 80 characters at most for better readability. To set line length of the file,
we should use [`textwidth`](https://neovim.io/doc/user/options.html#'textwidth') option:

> Maximum width of text that is being inserted.  A longer line will be
> broken after white space to get this width.  A zero value disables this
>

Add the following setting to your `init.vim` should work:

```vim
augroup my_textwidth
au!
autocmd FileType text,markdown,tex setlocal textwidth=80
augroup END
```

If we open an existing file, we will find that the file content isn't changed
automatically. It turns out that we have to manually format the existing texts.
We can use `gggqG` to format the entire document. When you type new text, it
will be correctly splitted to next line at textwidth.

References

+ https://thoughtbot.com/blog/wrap-existing-text-at-80-characters-in-vim
+ [Vim textwidth has no effect](https://stackoverflow.com/q/5136902/6064933)

# How to show the full path of current file?

You can use `{count}Ctrl-G` to show the full path of current buffer:

```
{count}CTRL-G           Like CTRL-G, but prints the current file name with
                        full path. If the count is higher than 1, the current
                        buffer number is also given.
```

To show only the full path of file, press `1` followed by `Ctrl-G`. To also
show its buffer number, use a count higher than `1`, e.g., `2<C-G>`.

References

+ [How can I see the full path of the current file?](https://vi.stackexchange.com/q/104/15292)

[^1]: `iw` means [inner word text object](http://vimdoc.sourceforge.net/htmldoc/motion.html#iw).
[^2]: `i"` is a [quote object](http://vimdoc.sourceforge.net/htmldoc/motion.html#i').
