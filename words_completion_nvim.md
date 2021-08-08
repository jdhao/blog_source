---
title: "English Words Completion inside Neovim/Vim"
date: 2019-04-26 02:15:05+0800
tags: [Vim]
categories: [Nvim]
---

<p align="center">
   <img src="https://blog-resource-1257868508.file.myqcloud.com/20190430093054.png" alt="">
</p>

Some words are hard to type, it is handy if Neovim can provide auto-completion
for the words we are typing. We can achieve word completion in Neovim in two
ways.

<!--more-->

# Native method

The first method is to use a dictionary file. The dictionary file is a text
file which contains plain English words, usually one word a line. In the Neovim
config file, add the following settings for
[dictionary](https://neovim.io/doc/user/options.html#'dictionary') option:

```vim
if has('unix')
    set dictionary+=/usr/share/dict/words
else
    set dictionary+=~/AppData/Local/nvim/words
endif
```

For Linux systems, the dictionary file is usually `/usr/share/dict/words`. For
Windows, there is no dictionary file. But you can copy the Linux dict file and
place it under `~/AppData/Local/nvim/`.

Then start editing a file, type the first several characters of a word, then
press
[<CTRL-X><CTRL-K>](https://neovim.io/doc/user/insert.html#i_CTRL-X_CTRL-K), the
completion items will be shown on a completion menu. Typing `<CTRL-X><CTRL-K>`
is cumbersome. You can add the following setting to
[complete](https://neovim.io/doc/user/options.html#'complete') option:

```vim
set complete+=k
```

Now, in insert mode, you can press `<CTRL-N>` to start auto-completion and move
selection to next word in the completion menu, while pressing `<CTRL-P>` will
move the selection to the previous word.

# Use deoplete for completion

The second method is to install
[deoplete-spell](https://github.com/deathlyfrantic/deoplete-spell). If you have
installed [deoplete](https://github.com/Shougo/deoplete.nvim), the only setting
you need is `:set spell`. Then you should be able to see the autocompletion
menu when you start typing characters.

There is also a plugin called [neco-look](https://github.com/ujihisa/neco-look)
which uses the `look` command on Linux system to provide auto-completion. But
it does not work natively on Windows.

# References

+ https://vim.fandom.com/wiki/Dictionary_completions
