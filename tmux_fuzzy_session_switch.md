---
title: "Fuzzy-switching Tmux Sessions with Ease"
date: 2021-11-20T23:21:30+08:00
draft: false
tags: [Tmux]
categories: [Linux]
---

During my work, I use multiple Tmux sessions to manage different projects. When
I create a new session, I usually give it unique name so that I know that the
project is about via the session name.

<!--more-->

# switching between sessions -- the basic way

Tmux has a builtin subcommand called `choose-session`. Initially, I add a key
binding for this command in my `.tmux.conf`:

```tmux
bind-key S choose-session
```

It works by showing a list of sessions, and you can switch between these
sessions by using `Ctrl-N` and `Ctrl-P`.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202111211332634.png" width="400">
</p>

One disadvantage with this method is that we can only switch between different
session one at a time. When I have multiple sessions, switching between them is
a pain.

# fuzzy-switching sessions with fzf

A more advanced way is to use fzf to filter sessions by their name and then
switch to the selected session.

First, we create a shell script `~/.tmux/tmux-switch-session.sh`[^1]:

```bash
#!/bin/bash
tmuxsessions=$(tmux list-sessions -F "#{session_name}")

tmux_switch_to_session() {
    session="$1"
    if [[ $tmuxsessions = *"$session"* ]]; then
        tmux switch-client -t "$session"
    fi
}

choice=$(sort -rfu <<< "$tmuxsessions" \
    | fzf-tmux \
    | tr -d '\n')
tmux_switch_to_session "$choice"
```

We need to give this script execution rights (solution found [here](https://stackoverflow.com/a/10038458/6064933)):

```bash
chmod +x ~/.tmux/tmux-switch-session.sh
```

Otherwise, when trying to run this script using key bindings, we get the
following error:

> /home/jdhao/.tmux/tmux_switch_to_session returned 126

Then add the following config to tmux conf:

```tmux
bind-key S run-shell -b "~/.tmux/tmux-switch-session.sh"
```

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202111211430279.png" width="400">
</p>


Note that `-b` option for `run-shell` subcommand is important, without which,
tmux will stuck and freeze forever, see also [this issue](https://stackoverflow.com/q/61799181/6064933).

# switching tmux sessions with menu

[This post](https://qmacro.org/autodidactics/2021/08/12/session-switching-with-the-tmux-menu/) shows how to switch sessions using a menu, which is also cool.

First, we create a script `~/.tmux/session-menu` (do not forget to give it
execution rights):

```tmux
#!/usr/bin/env bash

# See https://qmacro.org/autodidactics/2021/08/12/session-switching-with-the-tmux-menu/

tmux list-sessions -F '#S' \
  | awk 'BEGIN {ORS=" "} {print $1, NR, "\"switch-client -t", $1 "\""}' \
  | xargs tmux display-menu -T "Switch-session"
```

Then we create a key binding for it in tmux conf:

```tmux
bind-key ` run-shell -b "~/.tmux/session-menu"
```

It will show a small menu for you to switch between different sessions.
Pressing a number will switch to that session.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202111211429780.png" width="400">
</p>


# References

+ https://eioki.eu/2021/01/12/tmux-and-fzf-fuzzy-tmux-session-window-pane-switcher
+ switch session using popup: https://dev.to/waylonwalker/jump-to-running-tmux-sessions-with-fzf-4p77

[^1]: Source here: https://www.reddit.com/r/tmux/comments/9qluuf/comment/e8bml7m/
