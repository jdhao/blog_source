---
title: "How Do I Show the Current File Path In Neovim?"
date: 2019-07-31 23:10:27+0800
tags: [Vim]
categories: [Nvim]
---

I saw on [StackExchange](https://vi.stackexchange.com/questions/104/how-can-i-see-the-full-path-of-the-current-file/20768#20768)
that someone asked about how to see the full path of current file in Vim. I
think it would be helpful to write about how I do it.

<!--more-->

# Temporary solution

To show the full path of current file, you can press `{count}Ctrl-G`, that is,
a count bigger or equaling to 1, followed by `Ctrl-G`. If you press `1<C-G>`,
only the full path of the file will be shown. If you press a number bigger than
1, the buffer index of current file will also be shown. For more info, see `:h
Ctrl-G`.

You can also use [`expand()` ](https://neovim.io/doc/user/eval.html#expand())
function to see the full path of current file:

```
:echo expand('%:p')
```

In the above command, `%` is special character which represents the name of
current file. `:p` is a path modifier which is used to show the full path of
the file. Please see the `expand()` doc on other types of path modifiers.

# Permanent solution

If you want to show the current file path permanently so that you can see its
path whenever you like. A good place to put the file path is the [title
bar](https://www.computerhope.com/jargon/t/titlebar.htm). In order to do that,
you need to set the [`title`](https://neovim.io/doc/user/options.html#'title')
and [`titlestring`](https://neovim.io/doc/user/options.html#'titlestring').
Currently, I am using the following settings:

```
set title
set titlestring=%{hostname()}\ \ %F\ \ %{strftime('%Y-%m-%d\ %H:%M',getftime(expand('%')))}
```

It will show the hostname of Vim, then the complete path of the file and
followed by last modified time of the file.

The titlestring is just a string. You can use pre-defined modifier to show the
content of various variables and the results of various functions.  It seems a
bit complicated. But in fact, it is not so scary. It uses the same syntax as
the `statusline` (see `:h statusline`).

In the above setting for titlestring, we first show the hostname of the Nvim
using the [`hostname()`](https://neovim.io/doc/user/eval.html#hostname())
function. `%{}` is a special modifier, which will evaluate the expression
inside and subsititute the returned result as a string. `%F` is a modifier used
to show the full path of the file. The
[`getftime()`](https://neovim.io/doc/user/eval.html#getftime()) function is
used to show the last modified time of the current file.
[`strftime()`](https://neovim.io/doc/user/eval.html#strftime()) function is
used to convert the time to pre-defined format, that is `YYYY-mm-dd HH:MM`.

The end result is something like the following

![](https://blog-resource-1257868508.file.myqcloud.com/20190731231236.png)
