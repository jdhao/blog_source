---
title: "Spell Check in Nvim"
date: 2019-04-29 22:35:54+0800
tags: [Vim, writing]
categories: [Nvim]
---

In this post, I want to talk about how to enable the built-in spell check
feature in Nvim.

<!--more-->

# Enable spell check

First we need to add the spell check languages:

```vim
" spell languages
set spelllang=en,cjk
```

In the above config, we set the spell languages to `en` and `cjk`. The `cjk`
language is used to prevent the CJK characters from being marked as spell
errors. This is documented in the Nvim doc:

> If the name "cjk" is included, East Asian characters are excluded from
spell checking.  This is useful when editing text that also has Asian
words.

To enable spell check, just add `set spell` in your configuration file. Since I
do not want to use spell check all the time, I set up a toggle for this option
in normal and insert mode:

```vim
nnoremap <silent> <F11> :set spell!<cr>
inoremap <silent> <F11> <C-O>:set spell!<cr>
```

We can then press <kbd>F11</kbd> to toggle spell checking.

# Correcting spell errors in insert or normal mode

In insert mode, if you have typed some words which Vim thinks is miss-spelled,
an underline is shown below the words. To correct the error, you can press
`<C-x>` followed `s`. A completion menu will show a list of suggestions. You
can then choose the correct one.

In normal mode, to navigate between the possible spell errors, use the
following shortcut key:

+ `[s`: go to previous spell error
+ `]s`: go to next spell error

If you think a word is not a spell error, you can use `zg` to add the word to
your spell list. To correct an error, use `z=`. A list of candidate words will
be shown. You are prompted to enter a number to select the correct word. Or you
can use `1z=` to directly choose the first candidate.

# References

+ https://stackoverflow.com/q/640351/6064933
+ An auto-correctly plugin: https://github.com/sedm0784/vim-you-autocorrect
