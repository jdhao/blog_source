---
title: "Vim-like Settings for Tmux"
date: 2018-09-30
tags: [Nvim, Vim, Tmux]
categories: [Linux]
---

I am a [Neovim](https://neovim.io/) (or Nvim in abbreviation) user. In Nvim, we
use `Ctrl-W` as prefix for window-related operations. However, the default
prefix is `Ctrl-B` in Tmux. As a result, I often mistakenly press `Ctrl-W` when
trying to change panes inside Tmux, which is annoying. Why not change tmux
prefix to be more vim-like?

<!--more-->

Tmux does not create a configuration file under your home by default. You have
to create one yourself. First, we need to create a config file named
`.tmux.conf` under home directory:

```bash
touch ~/.tmux.conf
```

Then, we edit this file to customize tmux behaviours.

# Change prefix

To change prefix, add the following settings to `.tmux.conf`,

```tmux
unbind C-b
set-option -g prefix C-w
bind-key C-w send-prefix
```

# Split panes like Vim

By default, tmux use `<prefix> %` and `<prefix> "` to split window vertically
and horizontally respectively, which is rather obscure. We can change it to be
more Vim-like.

```tmux
# use <prefix> s for horizontal split
bind s split-window -v
# use <prefix> v for vertical split
bind v split-window -h
```

Then we can use `<prefix> s` and `<prefix> v` to create horizontal and vertical
split respectively.

# Vim-like pane navigation

In order to use the traditional Vim navigation key `HJKL` to switch between
different panes, use the following settings:

```tmux
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R
```

Then we can use `<prefix> h/j/k/l` to go to pane left/below/above/right.

We can also use <kbd>Alt</kbd>and arrow keys for pane switching,

```tmux
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D
```

Then we can press `Alt` followed by arrow key to switch panes.

# Resize panes

```tmux
# resize panes more easily
bind < resize-pane -L 10
bind > resize-pane -R 10
bind - resize-pane -D 10
bind + resize-pane -U 10
```

To resize pane in the horizontal direction, use `<prefix> <` or `<prefix> >`.
To resize pane in the vertical direction, use `<prefix> +` or `<prefix> -`.

# How to reload tmux config?

If you are in a tmux session, just press `<prefix>`, then use command `:source
~/.tmux.conf` to refresh the Tmux session. If you are outside of a Tmux session,
use `tmux source ~/.tmux.conf` to refresh tmux configurations.

Note: before changing the tmux prefix key, the old prefix still takes effect.
If you want to change the prefix inside a tmux session, you need to use the old
prefix.

# References

+ A good gist: https://gist.github.com/rssvihla/789284
+ [TMUX using HJKL to navigate panes](https://stackoverflow.com/q/30719042/6064933)
+ https://gist.github.com/spicycode/1229612
+ [changing .tmux.conf binding for prefix](https://superuser.com/q/580992/736190)
