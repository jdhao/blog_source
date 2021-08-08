---
title: "Edit Multiple Files at once in Neovim"
date: 2020-10-28T00:21:30+08:00
draft: false
tags: []
categories: [Nvim]
---

When I was in a folder and tried to edit multiple files using glob pattern with the
following Neovim command:

```vim
:edit *.vim
```

I was surprised to find that Neovim gives the following error message:

> E77: Too many file names.

<!--more-->

This is because the command `:edit` only supports editing one file. There are
several ways to open multiple files at once.

# Use argument list

We can Neovim's builtin in argument list feature to open multiple files. When
we open Neovim from command line with multiple files, argument list will be
filled with a list of file names. To replace the argument list with new file
names, we can use `:args {glob_pattern}` or `:next {glob_pattern}`.

For example, to open all files in current directory with extension `.vim`, use
`:args *.vim` or `:next *.vim`. You can then traverse the list of files using `:
next` and `:previous` command.

For more info, see `:h argument-list`.

# Define your own custom command

The second way is to define a custom `:Edit` command that can take glob
patterns. Something like the following should work:

```vim
function! MultipleEdit(p_list)
  for p in a:p_list
    for c in glob(p, 0, 1)
      execute 'edit ' . c
    endfor
  endfor
endfunction

command! -bar -bang -nargs=+ -complete=file Edit call MultipleEdit([<f-args>])
```

Then we can use `:Edit *.vim` or `:Edit *.vim *.py` to open multiple files.


# References

+ [How can I open multiple tabs at once?](https://vi.stackexchange.com/q/2108/15292)
+ [How to open multiple files matching a wildcard expression?](https://vi.stackexchange.com/q/2607/15292)
+ [How do I make vim open all files matching a pattern in different tabs?](https://stackoverflow.com/q/3468763/6064933)
