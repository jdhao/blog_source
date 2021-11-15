---
title: "Nifty Nvim/Vim Techniques That Make My Life Easier -- Series 8"
date: 2020-11-11T22:43:47+08:00
tags: [diff]
categories: [Nvim]
---

This post continues my [previous post](https://jdhao.github.io/2020/09/22/nifty_nvim_techniques_s7/)
on nifty Nvim/Vim techniques.

<!--more-->

# Use Neovim as man pager

The default pager used by `man` command on \*nix lacks syntax highlighting and
is not good for reading, searching. Why not turn nvim into the man pager? Just
add the following setting to your shell config file:

```bash
if [[ "$(command -v nvim)" ]]; then
    export EDITOR='nvim'
    export MANPAGER='nvim +Man!'
    export MANWIDTH=999
fi
```

See also `:h man.vim`.

Ref:

+ https://github.com/neovim/neovim/issues/9156

# Close other windows quickly?

When we are in a certain window, we may want to close all other windows. We may
go to the other windows and close them with `:quit`. It is a bit cumbersome.

The `:only` command is a much nicer way. It will close all the other windows
except the one we are in. There is also an equivalent shortcut: `<C-W> o` (that
is, `Ctrl-W`, followed by `o`).

Ref:

+ [Cancel split window in Vim](https://stackoverflow.com/a/4810928/6064933)

# Execute a macro in several lines.

Macro is a powerful way to edit texts with similar structures. To execute a
macro on several lines, we can use a line range if the lines are continuous.
For example, execute macro `a` for line 10 to 15, use:

```vim
10,15normal! @a
```

Or we can visually select the lines, and run the following command (note that
if you select these lines, and then press `:`, Nvim will insert `'<,'>`
automatically):

```vim
:'<,'>normal! @a
```

To execute a macro only on lines matching a certain pattern, run the following
command:

```vim
:g/pattern/normal! @a
```

Ref:

+ [In Vim, how do I apply a macro to a set of lines?](https://stackoverflow.com/q/390174/6064933)

# Copy URL under cursor into a register?

We can use `<cfile>` with `expand()` function get the URL under cursor (see `:h
<cfile>`). To copy the URL to unnamed register, use the following command:

```vim
let @" = expand('<cfile>')
```

The above method is not perfect, since `expand('<cfile>')` will also give you
results even if your cursor is on a normal words (non-URL).

A more sophisticated method would be using actual URL patterns and search the
current line to get a valid URL. A good URL pattern is provided by plugin
[vim-highlighturl](https://github.com/itchyny/vim-highlighturl) via
`highlighturl#default_pattern()` method. With this knowledge, here is a more
error-proof approach to get the current URL:

```vim
let @" = matchstr(getline('.'), highlighturl#default_pattern())
```

Ref:

+ [How do I copy the URL under the cursor?](https://vi.stackexchange.com/q/27571/15292)

# Get diff between two buffers or files

If we have two different versions of the same file and we want to find the
differences between them, how do we do it inside Neovim?

Suppose the two files are `manual-v1.md`, `manual-v2.md`, here is how to
compare them inside Neovim.

If you haven't start Nvim, you can run the following command:

```bash
nvim -d manual-v1.md manual-v2.md
```

This will start nvim in diff mode.

If you are already inside Neovim, first open `manual-v1.md` (`:e manual-v1.md`),
then open `manual-v2.md` in a vertical split window (`:vs manual-v2.md`)[^1].
Finally, run the following command to start comparing:

```vim
:windo diffthis
```

Ref:

+ [Take diff of two vertical opened windows in Vim](https://stackoverflow.com/q/4385515/6064933)

[^1]: Of course, you can use a horizontal split window, but vertical split window is better for comparing the two files, IMO.
