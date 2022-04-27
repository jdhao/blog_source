---
title: "How to Match Non-greedily in Nvim/Vim"
date: 2022-04-27T02:20:12+08:00
markup: markdown
tags: [Regex]
categories: [Nvim]
---

A short note on how to match [non-greedily](https://blog.finxter.com/python-regex-greedy-vs-non-greedy-quantifiers/) in Nvim/Vim.

<!--more-->

Vim's regex is slightly different from the usal regex we see.
Vim use `-` to indicate non-greedy match, for example, to match non-greedily for `a.*c`, we can use the following search:

```vim
\va.{-}c
```

Note that `\v` is used to simplify the code.
Otherwise, the command will become ugly and error-prone.

Here is a list of non-greedy syntax (the following assumes that `\v` is added):

+ `{-n,m}`: match n to m previous pattern, non-greedy
+ `{-n,}`: match at least n of previous pattern, non-greedy
+ `{-,m}`: match 0 to m of previous pattern, non-greedy
+ `{-}`: match 0 or more of previous pattern, non-greedy (non-greedy version of `*`)

For more info, read `:h non-greedy` inside Vim.
