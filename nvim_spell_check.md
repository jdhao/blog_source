---
title: "Spell Checking in Nvim"
date: 2019-04-29 22:35:54+0800
tags: [Vim, writing]
categories: [Nvim]
---

<details>
<summary><font size="2" color="red">Update log</font></summary>

+ <font color="blue">2021-10-17: add `spellsuggest` option.</font>
</details>

In this post, I want to talk about how to enable the built-in spell checking
feature in Nvim.

<!--more-->

# Enable spell check

First we need to add spell checking languages:

```vim
" spell languages
set spelllang=en,cjk
```

In the above config, we set spell languages to `en` and `cjk`. Item `cjk` is
used to prevent CJK characters from being marked as spell errors. This is
also documented in the [Nvim doc](https://neovim.io/doc/user/options.html#'spelllang'):

> If the name "cjk" is included, East Asian characters are excluded from
> spell checking. This is useful when editing text that also has Asian
> words.

Another option to tweak is [`spellsuggest`](https://neovim.io/doc/user/options.html#'spellsuggest'), I use the following settings:

```vim
" Show nine spell checking candidates at most
set spellsuggest=best,9
```

To enable spell checking, run command `:set spell`. I have set up two mappings
to toggle spell checking:

```vim
nnoremap <silent> <F11> :set spell!<cr>
inoremap <silent> <F11> <C-O>:set spell!<cr>
```

We can then press <kbd>F11</kbd> to toggle spell checking.

# Correct spell errors

In insert mode, if you have typed a word which Vim thinks is miss-spelled, an
underline is shown below. To correct this error, press `<C-x>` followed by `s`.
A completion menu will show a list of suggestions. You can then choose the
correct one.

In normal mode, to navigate between possible spell errors, use the following
shortcut:

+ `[s`: go to previous spell error
+ `]s`: go to next spell error

The built-in spell checker is not perfect and can create false positives
easily. If you think a word is not a spell error, you can use `zg` to add it to
your spell file. To correct an error, press `z=`. A list of candidate words
will be shown. You are prompted to enter a number to select the correct one. Or
you may use `1z=` to choose the first candidate directly.

# References

+ https://stackoverflow.com/q/640351/6064933
+ An auto-correctly plugin: https://github.com/sedm0784/vim-you-autocorrect
