---
title: "Nifty Nvim/Vim Techniques That Make My Life Easier -- Series 7"
date: 2020-09-22 22:39:15+0800
tags: []
categories: [Nvim]
---

This post continues my [previous post](https://jdhao.github.io/2019/12/21/nifty_nvim_techniques_s6/)
on nifty Nvim/Vim techniques.

<!--more-->

# Copy several lines to destination line

Use `:copy` command:

```
:[range]copy {address}
```

`[range]` is a line range  that has very flexible syntax, see `:h
cmdline-ranges` for the details. `{address}` is the destination address you
want to copy text to.

Let's see some concrete examples.
+ Copy line 1 to 3 to below line 11:
    ```
    :1,3copy 11
    ```

+ Copy three lines from current cursor position to the end of the file:
    ```
    :.,.+2copy $
    ```

Ref:

+ [Copy lines to other line in Vim](https://stackoverflow.com/q/42168713/6064933).

# Save state of all opened files and open them later

Sometimes, we want to restart Vim/Neovim, but do not want to lose the current
state since we may have opened a lot of files. A simple way is to use
`:mksession` command. It will create a file named `Session.vim`. Or you can
provide a custom name for it `:mksession my-sess.vim`.

When you start Vim/Neovim later, you can use `:source my-sess.vim` to restore
the state of previous state of Vim, with all the previous files opened.

There is also vim plugins such as
[vim-obession](https://github.com/tpope/vim-obsession) that helps you simplify
the management of sessions.

Ref:

+ [Restore opened files in Vim](https://stackoverflow.com/q/1416572/6064933).

# Delete lines matching/not matching a pattern?

To delete lines matching a pattern, we can use the `:global` command:

```vim
:%g/some pattern/d
```

Some explanation on this command:

+ `%`: specify the whole file. We can also specify a custom range, e.g., `2,10` (line 2 to line 10).  See also `: h cmdline-ranges`.
+ `some pattern`: The pattern we want to match. It can be normal text or regular expressions.
+ `d`:  For command `:delete`.

To delete lines not matching a pattern, we simply reverse the `:global` command
via `:global!`:

```
:%g!/some pattern/d
```

which will delete lines not matching `some pattern`.

`:g!` is also equivalent to `:vglobal` (or `:v`) command (think of it as `grep
-v`):

```
:%v/some pattern/d
```

Ref:

+ [How to delete all lines that do NOT contain a certain word in Vim?](https://superuser.com/q/265085/736190)

# Close the help window quickly?

According to answer [here](https://superuser.com/a/830966/736190), we can
simply the use the `:helpclose` command to close the help window, which is by
far the simplest way I have found.

# Jump to the first occurence of a search term?

When we search a pattern, we may want to go to the first occurence of the
search pattern. Just press `ggn` in normal mode:

+ `gg`: go the begining of the file
+ `n`: search forward for the pattern.

Ref:

+ [How do I go to the first search result in a file?](https://superuser.com/q/677940/736190)

# Keep only the lines matching a pattern from command output?

A vim command may output a lot of text, making it hard for us to find what we
want. For example, `:highlight` command will print a lot of lines of the
highlight group info.

We can use `:filter` to get only what we are interested. The syntax is:

```vim
:filter /pattern/ [cmd]
```

where `pattern` is the string pattern we want to search and `[cmd]` is a vim
command. If we want to ignore cases, we can add `\c` before pattern:

```
:filter /\cpattern/ [cmd]
```

For example, to search highlight groups that contains the word `search`
(ignoring the case), we use the following comamnd:

```vim
:filter /\csearch/ highlight
```

we only get the following three lines instead of the hundreds of lines:

```
IncSearch      xxx cterm=reverse gui=reverse guifg=#fe8019 guibg=#1d2021
Search         xxx cterm=reverse ctermfg=0 ctermbg=11 gui=reverse guifg=#fabd2f guibg=#1d2021
Searchlight    xxx links to ErrorMsg
```

Ref:

+ [How to grep in ex command output?](https://vi.stackexchange.com/q/5205/15292)
