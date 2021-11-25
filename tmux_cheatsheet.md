---
title: "Tmux Cheatsheet"
date: 2021-01-25T23:40:43+08:00
draft: false
tags: [Tmux]
categories: [Linux]
---

Some useful tips for using Tmux.

<!--more-->

In the following text, `<prefix>` means the prefix key for Tmux. If you haven't
changed it, it will `Ctrl-b` by default.

# Change layout

To change the layout of panes in a Tmux windows, press `<prefix><space>`. It
will change between different layouts.

We may also use command `select-layout` (or `selectl` for short) instead.
Possible layouts are: `even-horizontal`, `even-vertical`, `main-horizontal`,
`main-vertical`, `tiled`.

Ref:

+ [How to convert 2 horizontal panes to vertical panes in tmux?](https://superuser.com/q/493048/736190)

# zoom a pane

To temporarily zoom in and zoom out a pane, press `<prefix>z`. It will toggle
the zoom state of current pane.

# Create a pane that spans window width

I have two vertical pane side by side in a window, and I want to create a new
pane above these two panes, which will span the entire window width.
Essentially, I want to change from the following layout:

```
+--------------+--------------+
|              |              |
|              |              |
|              |              |
|              |              |
|              |              |
|              |              |
|              |              |
|              |              |
|              |              |
|              |              |
|              |              |
+--------------+--------------+
```

to the below layout:

```
+-----------------------------+
|                             |
+--------------+--------------+
|              |              |
|              |              |
|              |              |
|              |              |
|              |              |
|              |              |
|              |              |
|              |              |
|              |              |
+--------------+--------------+
```

By default, `split-window` command only spans the width of current pane. We can
use the `-f` option to make the new pane span the entire window width. The help
for `-f` says that:

> -f  -- create new pane spanning full window width or height

To make the new pane created above, we need to use `-b` option:

> -b  -- create new pane left of or above target pane

Combining the two options, we can use the following command to create what we
want:

```
# run the command after pressing <prefix>
:split-window -fbv
```

Similarly, if we have two pane one on the other, we want to create a new pane,
and get the following layout:

```
+-----------------+-----------+
|                 |           |
|                 |           |
|                 |           |
+-----------------+           |
|                 |           |
|                 |           |
|                 |           |
+-----------------+-----------+
```

we can use the following command:

```
:split-window -fh
```

Ref:

+ [How to add a horizontal split to tmux window that spans the whole width of the parent window?](https://unix.stackexchange.com/q/124310/221410)

# swap two panes

To swap two panes in Tmux, first we need to check their index: press `<prefix> + q`
to show the pane index. Then we can use the tmux command `swap-pane` to swap
the two panes (tmux command mode are activated by pressing `<prefix>`, followed
by `:`). For example, if we want to swap pane 0 with pane 2, use the following
command:

```
swap-pane -s 0 -t 2
```

Ref:

+ [How does one swap two panes in Tmux?](https://superuser.com/q/879190/736190)

# equalize two panes

To make two panes have the same width in side by side position, press `<prefix>
Alt-1`  or run command `select-layout even-horizontal`. To make two panes have
the same height and in top-down position, press `<prefix> Alt-2` or run command:
`select-layout even-vertical`.


Ref:

+ [How do I equally balance tmux(1) split panes?](https://unix.stackexchange.com/a/37754/221410)
