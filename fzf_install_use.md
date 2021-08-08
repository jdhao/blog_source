---
title: "Fzf Installation and Usage"
date: 2018-11-05T23:54:29+08:00
tags: [Nvim, shell, Bash, Zsh]
categories: [Linux]
---

<script id="asciicast-218493" src="https://asciinema.org/a/218493.js" async></script>

<!--more-->

[fzf](https://github.com/junegunn/fzf) is an interactive fuzzy file search tool
on the command-line. It is fast and powerful. In this post, I will introduce
its installation ans usage.

# Installation

## Two step install

We can use git to install fzf:

```bash
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```

The above command will install fzf under `~/.fzf/bin`.

If you want to enable fzf inside Neovim, add the following setting to Neovim
configuration (suppose your plugin manager is
[vim-plug](https://github.com/junegunn/vim-plug)):

```
Plug '~/.fzf'
```

## One step install

We can install fzf and enable it inside Neovim in one step. Use the following
configuration for Neovim:

```
Plug 'junegunn/fzf', { 'dir': '~/.fzf', 'do': './install --all' }
```

You can change the directory where you want to install fzf.

# How to use

## As Nvim plugin

Inside Nvim, we can search and open files quickly using the command provided by
fzf:

```
:FZF " find file under current directory
:FZF ~ " find file under HOME directory
```

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/nvim_fzf_open_file.gif" width="800">
</p>

For convenience, we can add shortcut mappings for the above commands:

```vim
nnoremap <silent> <leader>f :FZF<cr>
nnoremap <silent> <leader>F :FZF ~<cr>
```

You can use `Ctrl + N` and `Ctrl + P` or the arrow key to navigate through the
list of files found by fzf. To open the file in Neovim, fzf provides several
shortcut key:

+ `<Enter>`: open file in current window
+ `Ctrl + T`: open file in new [tab page](http://vimdoc.sourceforge.net/htmldoc/tabpage.html)
+ `Ctrl + X`: open file in new horizontal window
+ `Ctrl + V`: open file in new vertical window

To close the file search window, use <kbd>Esc</kbd> or
<kbd>Ctrl</kbd>+<kbd>C</kbd>.

## Use fzf with other command

You can also use fzf in conjunction with other command. For example, you can
use fzf and nvim together. Fzf is used to find the file and then open the file
with nvim:

```bash
nvim $(fzf)
```

## Use fzf alone

Fzf provide several command-line shortcut after installation:

+ `Ctrl + T`: paste the path of file or directory found on the command line
+ `Ctrl + R`: find history command and paste command on the command line
+ `Alt + C`: cd to specific directory

Note that `Ctrl + T` 和 `Alt + C` will only find file or folder under current
directory. If you can not find a file, please make sure that it exists under
current directory.

By default, fzf use `find` to search files. You can use the variable
`FZF_DEFAULT_COMMAND` to change the default search command. For example, if you
have installed [the silver searcher, AKA,
ag](https://github.com/ggreer/the_silver_searcher), you may want to use the
following setting in your `.bash_profile`:

```bash
export FZF_DEFAULT_COMMAND='ag --hidden --ignore .git -g ""'
```

## Autocompletion on command line

### Auto-complete file or directory names

When you are using `vim` and `cd`, you can activate file and directory fuzzy
completion with `**` as a trigger (press <kbd>Tab</kbd> to trigger
auto-completion):

```bash
vim **<TAB> # open file under current directory
vim ../**<TAB> # open file under parent directory
vim ~/**<TAB> # open file under $HOME

cd **<TAB> # go to a directory under current directory
```

The auto-completion function only supports a few commands. If you want to add
your own command, say, [pylint](https://www.pylint.org/), it is easy to set up.
Following the [guide here](https://github.com/junegunn/fzf/issues/536), you
need to add the below command to your `.bash_profile`:

```bash
complete -o bashdefault -o default -F _fzf_path_completion pylint
```

Do not forget to source your `.bash_profile`. Then you can trigger
auto-complete for pylint just like what you do with `vim` and `cd`:

```bash
# open some file under home directory in a recursive manner
pylint ~/**<TAB>
```

### Autocompletion for environment variables
Fzf can also auto-complete environment variables:

```bash
export **<TAB>
unalias **<TAB>
unset **<TAB>
```

## search hidden file by default

By default, fzf does not search hidden files. In order to search hidden files
by default, use the following setting (suppose that you use `ag`):

```bash
export FZF_DEFAULT_COMMAND='ag --hidden --ignore .git -g ""'
```

see [here](https://github.com/junegunn/fzf/issues/634) for more discussions.

## Make fzf work under Zsh on macOS

If you are using zsh on Mac with
[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh), zsh has a
[plugin](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/fzf)to
make fzf work with it.

First, add the following setting in `.zshrc`:

```bash
# suppose you have installed fzf to ~/.fzf, change it to what suits you
export FZF_BASE="$HOME/.fzf"
```

In the `.zshrc` plugin part, enable this plugin:

```
plugins=(
  ...
  fzf
)
```

Source `.zshrc` to make changes take effect. Fzf should work now on Zsh.

There is one gotcha, [`Alt + C` does not
work](https://github.com/junegunn/fzf/issues/164): it will just print the
character `ç`. If you are using [iterm2](https://www.iterm2.com/), it is easy
to fix. Open the iterm2 preference settings, go to `Profiles -> Keys`. In the
bottom right of the window, there are different buttons to choose the behaviour
of Option key. Just choose `Esc+` and everything should be fine.

![iterm2_alt_setting.png](https://blog-resource-1257868508.file.myqcloud.com/iterm2_alt_setting.png)
