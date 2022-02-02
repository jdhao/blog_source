---
title: "Using Diffs in Vim"
date: 2021-10-24T20:07:17+08:00
draft: false
tags: [diff]
categories: [Nvim]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202202020040611.jpg" width="600">
</p>


[Diff](https://man7.org/linux/man-pages/man1/diff.1.html) can be used to compare two versions of the same file to find the changes.
If you use vim, you can use `vimdiff` to compare files:

<!--more-->

```
vimdiff file1 file2
```

Actually vimdiff is just a wrapper around vim executable, you can do the same thing with vim using option `-d`:

```
vim -d file1 file2
```

So neovim just [removed this bloat](https://github.com/neovim/neovim/issues/1646#issue-51772204). To compare two files using neovim, run:

```
nvim -d file1 file2
```

Previously, Vim has been using external diff tool for file diffs, since [patch 8.1.0360](https://github.com/vim/vim/commit/e828b7621cf9065a3582be0c4dd1e0e846e335bf),
it beganto use [libxdiff](http://www.xmailserver.org/xdiff-lib.html), which is much faster and has more features.
According to discussion [here](https://github.com/vim/vim/pull/2732#issue-307057869), libxdiff is also used as a basis by Git as its internal diff library,
that is when you use `git diff` inside a git repository.

Since [this commit](https://github.com/neovim/neovim/commit/20620bae76deddd892df2d33f9e745efd5d8598b), the vim patch has been ported to neovim too.
You can activate the internal diff algorithm by update option [`diffopt`](https://neovim.io/doc/user/options.html#'diffopt'):

```vim
set diffopt+=internal
```

The default diff algorithm is myers diff. Other supported diff algorithm is:

```
                algorithm:{text} Use the specified diff algorithm with the
                internal diff engine. Currently supported
                algorithms are:
                myers      the default algorithm
                minimal    spend extra time to generate the
                           smallest possible diff
                patience   patience diff algorithm
                histogram  histogram diff algorithm
```

So to use histogram diff, add the following setting:

```vim
set diffopt+=algorithm:histogram
```

# References

+ [Removed features in Neovim](https://neovim.io/doc/user/vim_diff.html#nvim-features-removed).
+ [Is the git binary diff algorithm (delta storage) standardized?](https://stackoverflow.com/q/9478023/6064933)
+ https://vimways.org/2018/the-power-of-diff/ (it has some links on how indent-heuristic works)
