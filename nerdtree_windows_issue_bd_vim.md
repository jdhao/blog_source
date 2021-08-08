---
title: "Fixing the nerdtree Window Problem with `bd` Command in Nvim"
date: 2018-10-09 12:19:00 +0800
tags: [Nvim, Vim, Linux]
categories: [Note]
---

If you have a nerdtree file navigation window on the left of the current buffer
and use `:bd`[^1], you will find that the nerdtree window will occupy the whole
window.  When you open another buffer, you have to manually resize the nerdtree
window to a smaller size, which is super annoying.

How to prevent this behaviour?

<!--more-->

An easy way is to switch to another buffer first, and then delete the previous
buffer. We can create a shortcut for this:

```vim
nnoremap \d :bp<cr>:bd #<cr>
```

In the above command, `:bp` stands for `:bprevious`, which will change the
buffer to the previous buffer. `#` after the `:bd` command represents the last
used buffer, i.e., the buffer we want to delete.

There is also a Nvim plugin called
[vim-bufkill](https://github.com/qpkorr/vim-bufkill), which specifically
address this issue. After installing this plugin, you can use `:BD` to delete
the current buffer without closing the window.

# References
+ https://github.com/carlhuda/janus/issues/71
+ https://www.johnplummer.com/tools/macvim-nerdtree-janus-annoyances.html
+ https://github.com/scrooloose/nerdtree/issues/400
+ https://github.com/vim-airline/vim-airline/issues/460
+ https://superuser.com/questions/1131952/vim-close-buffer-not-quite-vim
+ https://stackoverflow.com/questions/1864394/vim-and-nerd-tree-closing-a-buffer-properly/
+ https://stackoverflow.com/questions/4465095/vim-delete-buffer-without-losing-the-split-window

[^1]: In Nvim, [`bd[elete]`](https://neovim.io/doc/user/windows.html#:bdelete) will delete the current buffer. But the window for the current buffer will also be closed.
