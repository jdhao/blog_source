---
title: "Tmux Questions and Trouble Shootings"
date: 2018-10-23 01:39:04+0800
tags: [Tmux]
Categories: [Linux]
---

In this post, I will summarize a list of questions related to Tmux
during the process of learning how to use it.

<!--more-->

# How to set the right status bar to show load average?

Show system load average and time at right of status bar

```tmux
set -g status-right "#[fg=red,bold]#(uptime | cut -d ',' -f 4-) #[fg=blue,bold][%Y-%m-%d %H:%M]#[default]"

# increase the length of status bar, or the characters won't fit
set -g status-right-length 100
```

You may need to change the part `cut -d ',' -f 4-` to `cut -d ',' -f 3-`.
Because, on my system, the output of `uptime` is something like this:

> 16:36:09 up 9 days,  5:30, 40 users,  load average: 31.12, 24.51, 21.68

With the above setting, the current load average and time will be shown on the
right side of tmux status bar.

# How does tmux color work?

You may use the following setting to set the color of some Tmux window component:

```tmux
# set the status bar background color to colour10
set -g status-bg colour10
```

The colors are from the 256 colors supported by the terminal.
we can use script below to show what these colors look like:

```bash
for i in {0..255}; do
    printf "\x1b[38;5;${i}mcolour${i}\x1b[0m\n"
done
```

Then, inside Tmux config file `~/.tmux.conf`, we can use a color with index
`<INDEX>` as `colour<INDEX>`.

See [here](https://superuser.com/q/285381/736190) for more discussions.

# Home and End key does not work inside Tmux?

Inside Tmux, the home and end key does not work. The reason is probably that
the terminal type inside Tmux conflict with terminal type set outside. Try to
use the following setting in `.tmux.conf`:

```
set -g default-terminal "screen-256color"
```

Also make sure that you do not manually set `TERM` variable in `.bash_profile`
or other shell configuration files. You should set `TERM` in your terminal
emulator configurations. This will most probably fix the issue.

See also reference [here](https://stackoverflow.com/q/18600188/6064933).

# What is terminfo and how to check the content of a term files?

For almost every terminal, there is terminfo file describing the ability of
this terminal. Usually, programs have to know the terminal type to send the
correct command to terminal. So the terminal or (terminal emulators) are
responsible for telling what they are capable of (i.e., what kind of terminal
they are). Then the various program depending on this information will read
this message and work accordingly.  That is why we need to set default term for
Tmux for it to work nicely with Neovim/Vim.

To see the content of a term file, you can use `infocmp` followed by the term
name. For example:

```
infocmp xterm-256color
```

The command above will print the info of term file `xterm-256color`. If you use
`infocmp` without any argument, it will print the info of term file currently
in use.

# Redraw window when switching from a smaller window to a bigger one?

When you attach to a session in two different terminal applications. You may
notice that some of the window space is filled with dots. That is because the
session window before is smaller than the current window. How to redraw the
current window so that it can fill the whole space?

You can use `tmux detach -a` inside the current session window. This command
will detach all session window except the current one.

There is another solution. When you attach to a session, you can use `-d`
option, which will detach all previous sessions opened elsewhere. The syntax
is:

```bash
tmux a -d -t <SESSION_NUM>
```

# How to copy text inside Tmux when `set -g mouse on`?

Press `<Shift>` and drag mouse to select text, the selected text will be copied automatically.

To copy a block of text when there are multiple vertical splits, press
`<Alt>`+`<Shift>` and drag mouse to select a rectangle text region.

Or you can temporarily zoom the current pane to make it occupy the whole window
and copy text. After you have copied the text, press `<Prefix> z` again to go
back to normal state.

# What does `-n` in `bind -n` mean?

By default, when you bind key to a Tmux command, you need to first press the
prefix key (the default prefix key is `Ctrl-b`). For example,

```
bind s split-window -v
```

In the above settings, `s` is binded to horizontally split a window. To invoke
it, you have to press `<prefix> s`. The `-n` options means that no prefix is
needed, thus we can directly press the keys to invoke a command. The Tmux man
page says:

```
bind-key [-nr] [-T key-table] key command [arguments]
                   (alias: bind)
             Bind key key to command.  Keys are bound in a key table.  By default (without -T), the key is bound in the prefix key table.  This table is used for keys pressed after the prefix key (for example, by default ‘c’
             is bound to new-window in the prefix table, so ‘C-b c’ creates a new window).  The root table is used for keys pressed without the prefix key: binding ‘c’ to new-window in the root table (not recommended) means
             a plain ‘c’ will create a new window.  -n is an alias for -T root.  Keys may also be bound in custom key tables and the switch-client -T command used to switch to them from a key binding.  The -r flag indicates
             this key may repeat, see the repeat-time option.
```

# There is a delay to exit Vim when pressing Esc key inside Tmux?

Tmux has a `escape-time` option to control how <kbd>Esc</kbd> is interpreted:

```
escape-time time.
    Set the time in milliseconds for which tmux waits after an escape is input to determine if it is part of a function or meta key sequences.  The
    default is 500 milliseconds.
```

You should set `escape-time` to a lower value to prevent it from being
interpreted as a Meta key. For example:

```
set -gs escape-time 10
```

See more discussions [here](https://github.com/neovim/neovim/issues/2035) and [here](https://www.johnhawthorn.com/2012/09/vi-escape-delays/).

# Difference between `set` and `set-option`?

`set` is an alias for `set-option`, see [here](https://stackoverflow.com/q/45017773/6064933).

# Difference between `bind` and `bind-key`?

`bind` is an alias for `bind-key`, see [here](https://superuser.com/q/581939/736190).

# `rz` and `sz` does not work inside Tmux?

`rz` and `sz` are not so popular and supporting it will add more code. So the
Tmux author decide to not support them. See
[here](https://github.com/tmux/tmux/wiki/FAQ#why-dont-xmodem-ymodem-and-zmodem-work-inside-tmux).

# Tmux hangs or freezes when there are a lot of output from one pane

When you open a Tmux session and start multiple vertical splits, if there are a
lot of output from one pane, for example, when you use `tar` to compress a
large amount of files, you may find that tmux is very slow to respond your
input in another pane split.

It seems that there is no good solution for this issue. You may need to change
your terminal emulator.

More discussions can be found [here](https://superuser.com/a/589896/736190), [here](https://github.com/tmux/tmux/issues/822) and [here](https://github.com/tmux/tmux/issues/609).

## References

+ [Is there any way to redraw tmux window when switching smaller monitor to bigger one?](https://stackoverflow.com/q/7814612/6064933)
+ [How can I check which terminal definitions are available?](https://unix.stackexchange.com/q/30058/221410)
+ https://www.tldp.org/HOWTO/Text-Terminal-HOWTO-16.html
+ http://man7.org/linux/man-pages/man5/terminfo.5.html
+ [Tmux mouse-mode on does not allow to select text with mouse](https://unix.stackexchange.com/q/332419/221410)
+ https://news.ycombinator.com/item?id=7758368
