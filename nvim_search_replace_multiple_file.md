---
title: "Search and Replace in Multiple Files in Vim/Neovim"
date: 2020-03-14 23:20:06+0800
tags: [Vim, search]
categories: [Nvim]
---

In Sublime-Text and other editors, we can press `Ctrl-Shift-F` to invoke the
search and replace UI. We can also accomplish this task easily in Vim or Neovim
with the built-in features. Here is how.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/8KSiBq.png" width="800">
</p>

There are two steps involved in this task. The first step is to find the files
containing the search pattern under the current project or directory. The
second step is to perform replacement for each file found in 1st step.

Depending on which Vim command to use, there are basically two ways to do this
task. For illustrating purposes, we will try to replace all `Neovim`
occurrences in [my nvim config repo](https://github.com/jdhao/nvim-config) to
`Nvim`.

# Use `vimgrep` or `grep` with `cfdo`

We can use `vimgrep` or `grep` command to populate the Vim quickfix list with
files matching our specific pattern. The `cfdo` command can then be utilized to
perform replacement operation for each file in the quickfix list[^1].

The difference between `vimgrep` and `grep` command is that `vimgrep` is
internal and provided by Vim, while `grep` command uses external programs to
search files depending on the platform (see `:h grepprg`).

## vimgrep

To find all files containing `Neovim` under current directory using `vimgrep`,
we can use the following the command:

```vim
:vimgrep /Neovim/gj **/*
```

In the above command, `Neovim` is our search pattern. The meanings of `g` and
`j` flags are as follows:

- g: Add all matches in a line to the quickfix list.
- j: Do not jump the cursor to the location of first pattern match.

The `**/*` specify the files to search for the pattern. In this case, it means
to search recursively under the current directory and for all files types[^2].
You can also search only in certain files types, for example, in Vim script:

```vim
:vimgrep /Neovim/gj **/*.vim
```

After this step, the quickfix will be populated with the matching files, lines
and column number (use `copen` to open the quickfix list):

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/8QoPCn.png" width="800">
</p>

## grep

If we use `grep` command, it is often useful to specify the external grep program
that we want to use via the `grepprg` option. For this, I highly recommend
[ripgrep](https://github.com/BurntSushi/ripgrep), which is one of the fastest
command line search tools. We need to add the following settings to Neovim or
Vim config:

```vim
set grepprg=rg\ --vimgrep\ --no-heading\ --smart-case
set grepformat=%f:%l:%c:%m
```

To search `Neovim` under current directory using `grep`, just use `grep
Neovim`.

## `cfdo`

After the quickfix list is filled with files containing the search pattern, we
now use `cfdo` command to perform replace operation on each file:

```vim
:cfdo %s/Neovim/Nvim/ge | update
```

The `cfdo` command can be followed by one command or a series of commands
(separated by `|`). In the above example, we first perform substitution, then
followed by `update` command so that the replacement change is saved.

# Use `args` and `argdo`

Another way to perform search and replace is to use `args` and `argdo` command.
The `args` command is used to build a list of files that we would like to
perform an action. `argdo` command is then used to perform this action on each
file in the argument list.

To add files to the argument list, we can use external tools like `grep` or
`ripgrep` to search files with backtick. For example:

```vim
:args `grep Neovim -l -r .`
```
or

```vim
:args `rg Neovim -l`
```

One issue with backtick is that it does not work on Windows. You can only use
the above command on Linux or macOS.

After adding all files containing the pattern to argument list, we then use
`argdo` to perform replacement (works similarly to `cfdo` command):

```vim
:argdo %s/Neovim/Nvim/ge | update
```

# References

+ [Search and replace all files in current folder](https://vi.stackexchange.com/q/2776/15292).
+ [set up grepprg for ripgrep](https://github.com/BurntSushi/ripgrep/issues/425#issuecomment-381446152).
+ https://gabri.me/blog/multiple-files-search-and-replace-in-vim
+ https://chrisarcand.com/vims-new-cdo-command/
+ https://thoughtbot.com/blog/lists-vim-and-you
+ [Ripgrep cheatsheet](https://jdhao.github.io/2020/02/16/ripgrep_cheat_sheet/).
+ http://vimcasts.org/episodes/using-argdo-to-change-multiple-files/

[^1]: The `cfdo` command was added in [Vim 7.4.858](https://github.com/vim/vim/commit/aa23b379421aa214e6543b06c974594a25799b09). Your Vim version should be newer than that.
[^2]: For more info on the glob pattern, see `:h starstar-wildcard` inside Vim.
