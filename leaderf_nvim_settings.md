---
title: "LeaderF settings"
date: 2020-08-26 23:24:20+0800
tags: []
categories: [Nvim]
---

[LeaderF](https://github.com/Yggdroot/LeaderF) is Vim/Neovim plugin to
fuzzy-search files, colorschemes, tags etc. quickly. It works across different
platforms flawlessly. Here are some of its settings to address minor issues.

<!--more-->

# File names with non-ASCII characters shown as escaped number?

When I am inside a git repository and use `:Leaderf file`, I find that files
whose names containing non-ASCII characters are shown as escaped numbers. For
example, `测试.md` is shown as `\346\265\213\350\257\225.md`.

This is because `Leaderf file` use `git` to index files under git repos by
default. Git shows non-ASCII characters as escaped numbers. To fix this, run
the following command:

```bash
git config --global core.quotePath false
```

Ref:

+ https://github.com/Yggdroot/LeaderF/issues/203
+ [git not displaying unicode file names](https://stackoverflow.com/q/34549040/6064933)

# Can not search files in a git submodule

In a git repo that has
[submodules](https://github.blog/2016-02-01-working-with-submodules/) in it, I
find that command `Leaderf file` can not list files under these submodules.

Why? Because in a git repository, [LeaderF will the command `git ls-files` to
list files](https://github.com/Yggdroot/LeaderF/issues/99), which by default
will exclude files inside git submodules.

If we are using Git 2.11 or later, we can set the following options:

```vim
let g:Lf_RecurseSubmodules = 1
```

Under the hood, it uses the [`--recurse-submodules`](https://stackoverflow.com/a/40311391/6064933) option to list files in submodules.

If we are using older version of Git, we can use the following option:

```vim
let g:Lf_UseVersionControlTool = 0
```

It will disable using `git` to index files.

# Do not show fancy filetype icons

By default, LeaderF will try to show fancy filetype icons before file names
when we use `:Leaderf file`. However, due to font issues, some of the icons may
not show properly. To disable this feature, add the following option:

```vim
let g:Lf_ShowDevIcons = 0
```

# Set working directory for git repository

In a Git repository, I want to always search files under the project root, even
though my current working directory may not be in the project root. We can set
`g:Lf_WorkingDirectoryMode` to customize how the project root directory is
found.

```vim
let g:Lf_WorkingDirectoryMode = 'a'
```

If we use the above setting, LeaderF will find ancestor of current directory
that contains `.git` or other root indicators. See `:h
g:Lf_WorkingDirectoryMode` for more help.

Ref:

+ https://github.com/Yggdroot/LeaderF/issues/702

# Refresh file indexing every time we use LeaderF?

I created a new source file under the current project. After a while, I started
LeaderF and found that I can not search the newly created file without
refreshing LeaderF (i.e., press `F5` to refresh). This is because LeaderF will
cache your project files once you start using LeaderF for the first time, to
save some precious time. To disable this behavior, that is, to indexing files
every time you invoke LeaderF, add the following setting:

```vim
let g:Lf_UseMemoryCache = 0
```

For very large project, it may take quite some time to finish indexing files.
So you should use this option with care.

Ref:

+ https://github.com/Yggdroot/LeaderF/issues/161
