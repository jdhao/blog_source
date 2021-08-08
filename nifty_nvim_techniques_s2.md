---
title: "Nifty Little Nvim Techniques to Make My Life Easier -- Series 2"
date: 2019-04-17 00:49:06+0800
tags: [Vim]
categories: [Nvim]
---

This is the series 2 of the nifty Nvim techniques. For series 1, see [here](https://jdhao.github.io/2019/03/28/nifty_nvim_techniques_s1/).

<!--more-->

# How to add a newline after some pattern?

In other styles of regular expressions, `\n` is used to denote a newline
character. In nvim, it is a bit complicated. In search, you can use `\n` to
search a newline. But in replace, you need to use `\r` to insert a newline.
`\n` in replace mode will insert `NULL` character (shown as `\@`) in nvim.

References

+ [How to replace a character by a newline in Vim](https://stackoverflow.com/q/71323/6064933)
+ [Vim - how to replace one new line \n with two \n's](https://unix.stackexchange.com/q/247329/221410)
+ `:h sub-replace-special`

# How to output the value of a nvim option or variable to current buffer?

For a variable, to output its value to the buffer. You have two options:

1. Use `:put=` followed by variable name in NORMAL mode. For example, if you
   want to put the value of `airline_theme` to current buffer, use
   `:put=airline_theme`.
2. Use `<C-R>=` in INSERT mode. In INSERT mode, first press
   <kbd>Ctrl</kbd>+<kbd>R</kbd>, then press <kbd>=</kbd>, input a variable
   name, e.g., `airline_theme`.


For a Nvim option, the operation is similar except that you need to add a `&`
before option. For example, to put the value `timeoutlen` into current buffer,
you can either use `:put=&timeoutlen<CR>` or use `<C-R>=&timeoutlen<CR>`.


References

+ [how to write value of variable into current edit file in vim script](https://stackoverflow.com/q/18200435/6064933)
+ [How to copy the value of a vim option to a register](https://stackoverflow.com/q/23604388/6064933)

# How do I set multiple options in one autocmd?

Based on some event or the filetypes, we may want to set up several options at
once. How do we do it in Vim?

For example, when we open a terminal inside Nvim, reset the `number` and
`relativenumber` settings, we can use the following command:

```
autocmd TermOpen * setlocal nonumber norelativenumber
```

References

+ [Multiple autocommands in vim](https://stackoverflow.com/q/1413285/6064933)

# How do I open a terminal window inside neovim?

For neovim, to open a vertical terminal window, the following works:

`:vnew term://bash`
`:vsplit term://bash`
`:vnew term://zsh`
`:vsplit term://zsh`

To open a horizontal window, remote `v` in the above command. More
documentation can be found by using :h :terminal inside neovim.

References

+ [Opening a vertical terminal in Vim 8.1](https://unix.stackexchange.com/q/444682/221410)


# How do I use all kinds of shortcut in vim command mode?

When we enter the vim command mode by using `:`, we can not use the many
shortcut provided by vim (we are in INSERT MODE in some sense), which makes
command editing inefficient. Vim also provides a more powerful command window,
which you can open by either press `<C-F>` in command mode or press `q:` in
NORMAL mode window.

In the command mode, a list of history command is shown. To execute a specific
command, move the cursor to that line and press `<CR>` in NORMAL mode. The
command will be executed as if from the window where the command window is
started. To edit a command, move to the line and start editing using all the
shortcut provided by Vim.

By default, you will be in NORMAL mode when you open the command window. To
open the command window and start inserting command automatically, we will
employ the `CmdwinEnter` event provided by Vim:

```
autocmd CmdwinEnter [:] startinsert
```

## How to quit command window

+ `:quit`
+ Press `<C-c>`

References

+ [How can I move around in the Vim command line?](https://stackoverflow.com/q/2075569/6064933)

# How do I create folds in vim configuration?

For long Vim configuration files, it is hard to grasp the structure of the
whole file and to navigate the file. Thus it is desirable to create fold for
each section of the vim configuration.

To achieve this, we can employ the
[modeline](https://neovim.io/doc/user/options.html#modeline) to set the
fold-related options for Vim config file only.

First, add the following line to the first or last line of your Vim
configuration:

```
" Vim: set fdm=marker fmr={{{,}}} fdl=0 fdls=-1:
```

In the above setting, we set the `foldmethod` (`fdm`) to `marker` and set
`foldmarker` (`fmr`) to `{{{,}}}`. The start and end fold markers are separated
by a comma. So in this example, the start and end fold marker are respective
`{{{` and `}}}`. Then Nvim will try to close the fold automatically based on
the fold marker. If you use modeline, you must turn on the `modeline` options
if it is off.

## create a fold

In your vim configuration, in order to create a section `Nvim builtin options
and settings`, you can use the following boilerplate code:

```
" Nvim builtin options and settings {{{
    " put all your settings inside here
    set number
    set relative number
    " etc......
" }}}
```

When you save the file and open it again, you will find that the fold takes
effect.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190417005923.png" width="400">
</p>

References

+ [Is there a best practice to fold a vimrc file](https://vi.stackexchange.com/q/3814/15292)
+ [How do I fold my .vimrc non-temporarily using vim code-folding? (sp13-vim)](https://superuser.com/q/902219/736190)

# How to find the doc of some settings if we do not know its exact key-words?

I saw some vimrc which have the following settings:

```
syntax spell toplevel
```

Since I donot know the keyword for this setting, I can not go to it directly
with `:h KEYWORD`. We can use `helpgrep` instead, which will put a list of
related results in a quickfix window. In this case, we can use `:helpgrep
toplevel` and it returns 7 results. You can open the quickfix window with
`:cwindow` command and then navigate a window using `:cprevious` and `:cnext`
command.

References

+ vim doc: `:h helpgrep`

# How do I set the working directory to where the currently opened file is?

If you are editting a file, how to set vim's working directory to the folder
the file reside? In Vim, we can use `:cd %:h`.  `%` represents the current file
and `:h` is a modifier which gets the path to the current file. For more info
about file modifiers, see `:h cmdline-special`.

References

+ [change working directory to current opened file](https://vi.stackexchange.com/q/3674/15292)
+ [Change working directory to currently opened file](https://stackoverflow.com/q/3847796/6064933)
