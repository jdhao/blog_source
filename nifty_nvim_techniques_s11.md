---
title: "Nifty Nvim/Vim Techniques That Make My Life Easier -- Series 11"
date: 2021-11-22T23:27:57+08:00
draft: false
tags: [Regex]
categories: [Nvim]
---

This post continues my [previous post](https://jdhao.github.io/2021/06/17/nifty_nvim_techniques_s10/) on a series of nifty
Nvim/Vim techniques.

<!--more-->

# Move lines matching a pattern together?

Use command `:g/pattern/normal ddGp`.

Here we use the `:global` command. It will apply the command `:normal ddGp`
(which is deleting current line and paste it at the bottom) for each line
matching `pattern`.

Another way to do this is to use `:move` together with `:global` command:

```
:[range]m[ove] {address}          *:m* *:mo* *:move* *E134*
          Move the lines given by [range] to below the line
          given by {address}.
```

Now the complete command to do this task is `:g/pattern/m$` (`$` means the last
line of a buffer).

At the end, all lines matching `pattern` will be moved to the end of the
buffer.

Ref:

+ https://stackoverflow.com/q/6553963/6064933

# Show trailing whitespace

We need to first define a highlight group for it and then match a pattern to
use this highlight group:

```vim
highlight TrailingWhitespace ctermbg=red guibg=red

call matchadd("TrailingWhitespace", '\v\s+$')
```

We'd better use single quote pattern to avoid backslash hell, otherwise,
escaping the pattern will be a pain. If we use double quote, `\s` won't work,
we have to escape backslash, see also `:h literal-string`.

# Flip a 01 string quickly

To flip a 01 string (i.e., turn 0 to 1 and turn 1 to 0), we can use
`sub-replace-expression` which is really powerful:

```vim
:s/\v(0|1)/\=submatch(0)==0?1:0/g
```

Ref:

+ https://stackoverflow.com/a/69748382/6064933

# Search lines not starting with a pattern

This is a perfect example where using [look around regex](https://jdhao.github.io/2018/10/18/regular_expression_nvim/#lookaround-in-nvim)
solves it neatly. For example, to search for lines not starting with `foo`, use
the following search expression:

```vim
/\v^(foo)@!
```

A teardown of this expression:

+ `\v`: magic regex in vim, see also `:h \v`. We use `\v` to simplify writing regex in vim and make it more like PCRE.
+ `^`: start  of a line
+ `(foo)@!`: `@!` is the vim syntax for [negative lookahead](https://www.regular-expressions.info/lookaround.html). This expression
means that the previous expression should not be followed by `foo`, we group
`foo` in Vim using parentheses.

Ref:

+ https://stackoverflow.com/q/1456061/6064933
