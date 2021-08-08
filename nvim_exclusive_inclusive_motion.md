---
title: "Exclusive and Inclusive Motion in Neovim/Vim"
date: 2019-05-18 01:18:28+0800
tags: [Vim]
categories: [Nvim]
---

# Introduction

Suppose that we have the following text in normal mode (cursor is indicated by
`^`):

```
hello world
^
```

If we use `dw`, we delete `hello<Space>`[^1] and only `world` is left; if we
use `de`, `hello` is deleted and `<Space>world` is left. Have you ever wondered
about why `dw` do not delete `w` while `de` will delete the `o` in `hello`? It
seems that motion `e` and `w` are somewhat inconsistent in their behaviours. It
turns out that it has something to do with the exclusivity of motions in Vim.

<!--more-->

# Exclusive and inclusive motion explained

Most Vim motions are either exclusive or inclusive. This nature of a motion is
best revealed when you perform an operation (these operations include, but are
not limited to `c`, `d`, `y`) followed by these motions. `Inclusive` means that
the texts between the start and end position of the cursor is used for that
operation, while `exclusive` means that the **last character near the end of
current buffer**[^2] is not included in the operation.

If you had known the above difference between the exclusive and inclusive
motion, you would not be surprised about the so called *inconsistency* between
`dw` and `de`. The reason is that `w` is an exclusive motion and `e` is an
inclusive motion. Someone has asked on the Stack Exchange site on [why backward
delete such `db` can not delete the character in the cursor
position](https://superuser.com/questions/771897/vim-delete-motions-include-under-the-cursor).
That is also because `b` is an exclusive motion so that the last character
toward the end of the buffer is not included.

# Change the exclusivity of a motion

To force an exclusive **motion** to be inclusive or vice versa, you can prepend
the **motion** by `v` (from Vim doc):

```
v       When used after an operator, before the motion command: Force
        the operator to work characterwise, also when the motion is
        linewise.  If the motion was linewise, it will become
        |exclusive|.
        If the motion already was characterwise, toggle
        inclusive/exclusive.  This can be used to make an exclusive
        motion inclusive and an inclusive motion exclusive.
```

Take the case in the first paragraph for an example, `dvw` will delete
`hello<Space>w` (`w` becomes inclusive) and `dve` will delete only `hell` (`e`
becomes exclusive). To find more detailed doc on this behaviour, read `:h
exclusive` carefully.

# References

+ https://stackoverflow.com/questions/21686886/making-backwards-motions-always-inclusive
+ https://stackoverflow.com/questions/16815627/in-vim-why-is-the-behavior-of-w-word-selector-inconsistent-when-preceded-by-d
+ https://stackoverflow.com/questions/23041326/why-dmotion-command-is-not-consistent-with-motion-command-in-vim/56179473#56179473

[^1]: `<Space>` indicates a space character.
[^2]: You need to interpret this carefully. When you do forward motions, the last character is where the motion lands. If you do backward motions, the last character in this context means the character your motion starts at.
