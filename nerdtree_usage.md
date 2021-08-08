---
title: "Intro to Nvim File Explorer Plugin Nerdtree"
date: 2018-09-10 11:44:43 +0800
tags: [Vim]
categories: [Nvim]
---

[Nerdtree](https://github.com/scrooloose/nerdtree) is a file explorer for
Vim/Neovim. It will generate a file navigation window just like an IDE. We can
operate on files easily in the nerdtree window. In this post, I will summarize
some of its configurations and usages.

<!--more-->

# Install

We can use [vim-plug](https://github.com/junegunn/vim-plug) to install nerdtree：

```
Plug 'scrooloose/nerdtree'
```

Then install this plugin in Nvim using command `:PlugInstall`.

# Configuration

In normal mode, you can use `:NERDTreeToggle` to open the nerdtree windows.
Nerdtree does not provide a shortcut for this command by default. In order to
quickly open or close the file navigation window, we can add a shortcut to this
command. For example, imitating the way Sublime Text opens the side bar, we can
use this setting:

```
nnoremap <silent> <C-k><C-B> :NERDTreeToggle<CR>
```

If you want to open the nerdtree window when opening up Nvim, but put the
cursor in the file-editing window, you can use the following setting:

```
augroup nerdtree_open
    autocmd!
    autocmd VimEnter * NERDTree | wincmd p
augroup END
```

For more info, see [here](https://stackoverflow.com/q/24808932/6064933).

# Directory operation

## Create child file or dir under a directory

First, move the cursor to the directory, press `m`(stand for menu), then press
`a` to create a child node which can be a file or a directory. To create a
directory, you need to append `/` behind the node name. See
[here](https://sookocheff.com/post/vim/creating-a-new-file-or-directoryin-vim-using-nerdtree/)
for more info.

## How to exit a menu？

When you press `m` to open the file operation menu for a directory, if you do
not want to perform any operation, you can press `<ESC>` or `Ctrl-C` to exit
this menu. See [here](https://github.com/scrooloose/nerdtree/issues/562).

## go up one level of directory

To show parent directory of current node, press `u`.

## Change root to the directory where the cursor is

To change the root directory to where the cursor resides, press <kbd>C</kbd>.

## Open/close child directory of current directory recursively

+ Open recursively：`O`
+ Close recursively：`X`

## Move the cursor to the first/last node which has the same level with current node

+ Move to first node of same level：`K`
+ Move to last node of same level： `J`

A *node* can be a file or directory.

## Move the cursor to the previous/next node which has the same level with current node

+ Move to previous node of same level：`Ctrl + k`
+ Move to next node of same level：`Ctrl + j`

# File node operations

## How to open a file in new Nvim window？

To open file in a new window in vertical direction, use `i`. To open file in a
new window in horizontal direction, use `s`.

We can also use `gi` or `gs`. The function is the same except that the cursor
is still in the nerdtree file navigation window. See reference
[here](https://stackoverflow.com/q/20645556/6064933).

## How to rename or move files?

In the nerdtree window, move the cursor to the node that you want rename, then
press `m`. In the opened up menu, press `m` again. In Neovim window below, you
will see prompt to give the path of the new file. Just change the file name.

If you want to move the file, the operation is the same: just input a new path
for this file (including the file name).

## How to reveal or show current file in nerdtree window

In normal mode, use `:NERDTreeFind` command to reveal the position of current
file in the file tree. There is no shortcut for this command. According to
solution provided [here](https://stackoverflow.com/q/7692233/6064933), we can
use the following mappings:

```
nmap <leader>nf :NERDTreeFind<CR>
```

What is `<leader>`? See [here](http://learnvimscriptthehardway.stevelosh.com/chapters/06.html)
for an explanation.

# Miscellaneous

## How to yank the path of a node in nerdtree window

When we are navigating through the nerdtree window, we may want to copy the
full path or the relative path of current node. Nerdtree provide a custom
function [`NERDTreeAddKeyMap`](https://github.com/scrooloose/nerdtree/blob/master/doc/NERDTree.txt#L1126)
to achieve what we want.

First, we need to create a file, e.g., `mymapping.vim`, under the nerdtree
install directory. If you are using Neovim and use vim-plug to manage your
plugin, the directory is likely to be
`~/.local/share/nvim/plugged/nerdtree/nerdtree_plugin/` or something similar.
Anyway, create file `mymapping.vim` under this directory and edit it.

To yank the full path of node using shortcut `yy`, we can use the following
settings:

```
call NERDTreeAddKeyMap({
        \ 'key': 'yy',
        \ 'callback': 'NERDTreeYankFullPath',
        \ 'quickhelpText': 'put full path of current node into the default register' })

function! NERDTreeYankFullPath()
    let n = g:NERDTreeFileNode.GetSelected()
    if n != {}
        call setreg('"', n.path.str())
    endif
    call nerdtree#echo("Node full path yanked!")
endfunction
```

Similarly, to yank the relative path (relative to current root) of current code
using `yr`, we can use the following settings:

```
call NERDTreeAddKeyMap({
        \ 'key': 'yr',
        \ 'callback': 'NERDTreeYankRelativePath',
        \ 'quickhelpText': 'put relative path of current node into the default register' })


function! NERDTreeYankRelativePath()
    let n = g:NERDTreeFileNode.GetSelected()
    if n != {}
        call setreg('"', fnamemodify(n.path.str(), ':.'))
    endif
    call nerdtree#echo("Node relative path yanked!")
endfunction
```

For the meaning of `fnamemodify()`, see
[here](http://learnvimscriptthehardway.stevelosh.com/chapters/40.html).

Ok, that is all the settings. Then you can use `yy`/`yr` to copy the
full/relative path of node under the cursor when you are in the nerdtree file
explorer window.

## How to ignore files and folders

Nerdtree shows all the files and directories under current root by default. If
you want to ignore certain files and folder, e.g., `*.pyc` file and
`__pycache__` directory, you can use the following settings:

```vim
" the ignore patterns are regular expression strings and seprated by comma
let NERDTreeIgnore = ['\.pyc$', '^__pycache__$']
```

You can use regular expression for the ignore pattern and separate several
patterns by commas.

## How to show current root as relative path from `$HOME` in status bar?

Nerdtree has a setting `NERDTreeStatusline` for statuslines. Use the following
setting in your Nvim config:

```vim
let NERDTreeStatusline="%{exists('b:NERDTree')?fnamemodify(b:NERDTree.root.path.str(), ':~'):''}"
```

I got my inspiration from [here](https://github.com/vim-airline/vim-airline/issues/271),
[here](https://stackoverflow.com/q/4525261/6064933) and also the nerdtree documentation.

# References

+ Ignore files in nerdtree:
    + https://github.com/scrooloose/nerdtree/issues/817
    + https://stackoverflow.com/questions/5601749/how-to-filter-out-files-by-extension-in-nerdtree
    + https://framagit.org/marien.fressinaud/dotfiles/commit/65ded2a418a06e8dd05a0ef3bb70b69c2130b356
+ Yank the node path, https://stackoverflow.com/questions/16368771/copy-path-file-with-nerdtree-vim-plugin
