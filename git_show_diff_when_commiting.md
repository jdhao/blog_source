---
title: "Show Git Diff When Doing Git Commit"
date: 2021-07-24T11:07:16+08:00
draft: false
tags: [Nvim, Vim]
categories: [Git]
---

I saw on YouTube [a video](https://www.youtube.com/watch?v=xv9cp7NyK5Y) made by
[TJ DeVries](https://github.com/tjdevries) on how to contribute to the [neovim
project](https://neovim.io/). There is a scene where he commit via `git commit`,
and whoa, a Neovim instance appeared with three split, with top left the commit
message area, top right the git-status area, and on the right the git-diff info,
which is really cool.

<!--more-->

A little background, the default git commit only shows status info without the
diff info. For commit related to changes in several files, it is difficult to
remember all the diffs and summarize them in the commit message.

I searched a bit and figured out how to do it in Neovim.

# Native git options

First, make sure that commit message editor is set to Vim or Neovim:

```bash
git config --global core.editor nvim
```

We can add `--verbose/-v` option when commiting to show the diff info:

```bash
git commit -v
```

Better, to set it permanently (for Git version above 2.9.0):

```bash
git config --global commit.verbose true
```

With the above config, the diff info will be shown below the status info. For
short diffs, it is acceptable, while for longer diffs, it is still not easy to
check.

# Using a plugin

[committia.vim](https://github.com/rhysd/committia.vim) is a plugin that
re-arrange the commit window for easier writing of commit messages. When you
run `git commit`, it will automatically show a three-split window like the
following:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20210724122638.jpg" width="800">
</p>

# References

+ `git commit -v` by default: https://stackoverflow.com/q/5875275/6064933
+ How do I make commit diff appear in commit message while I'm editing it (with Vim)?: https://stackoverflow.com/a/26457364/6064933
+ https://gist.github.com/aroben/d54d002269d9c39f0d5c89d910f7307e
+ https://vimways.org/2018/vim-and-git/
