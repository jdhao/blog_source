---
title: "Better Zsh history"
date: 2021-03-24T22:56:53+08:00
draft: false
tags: [Zsh]
categories: [Linux]
---

My notes on setting up history-related features for zsh.

<!--more-->

# History settings

Some env variable controlling the behaviour of history feature:

```zsh
# the detailed meaning of the below three variable can be found in `man zshparam`.
export HISTFILE=~/.histfile
export HISTSIZE=1000000   # the number of items for the internal history list
export SAVEHIST=1000000   # maximum number of items for the history file

# The meaning of these options can be found in man page of `zshoptions`.
setopt HIST_IGNORE_ALL_DUPS  # do not put duplicated command into history list
setopt HIST_SAVE_NO_DUPS  # do not save duplicated command
setopt HIST_REDUCE_BLANKS  # remove unnecessary blanks
setopt INC_APPEND_HISTORY_TIME  # append command to history file immediately after execution
setopt EXTENDED_HISTORY  # record command start time
```

In the above config, option `EXTENDED_HISTORY` makes sure that zsh record the
time that a certain command is executed. By default, command typed in a shell
session are saved in the internal command history list. When the shell exits,
commands in the internal history list will be written to `HISTFILE`. Option
`INC_APPEND_HISTORY_TIME` changes this behavior: right after you have executed
some command, it will be written to the history file, along with the time spent
to execute it. Refer to `man zshoptions` for more details.

Ref:

+ https://www.soberkoder.com/better-zsh-history/ (note that `HISTTIMEFORMAT` mentioned is not supported by zsh, it is Bash specific)

# `history` command shows only 16 items?

In zsh, if I use `history` command, only 16 items are shown, which is far less
than the actual command history stored in `HISTFILE`. Why? According to the doc
(`man zshbuiltins`), history command is just an alias to `fc -l`. The doc for
`fc` command says:

>
> fc -l [ -LI ] [ -nrdfEiD ] [ -t timefmt ] [ -m match ]
             [ old=new ... ] [ first [ last ] ]
>
> If first is not specified, it will be set to -1 (the most recent event), or to
> -16 if the -l flag is given.  If last is not specified, it will be set to first,
> or to -1 if the -l flag is given.

So when we use command `history`, what we actually use is `fc -l -16 -1`, so
the most recent 16 items in history are shown.

We can create an alias to change this:

```
# show all the history stored.
alias history="fc -l 1`
```

Ref:

+ [zsh history is too short](https://stackoverflow.com/q/26846738/6064933)
+ [ZSH only displaying last 16 or so commands with history. HISTSIZE & SAVEHIST are 500](https://superuser.com/q/160342/736190)
+ [ZSH - output whole history?](https://superuser.com/q/232457/736190)

# Search history command more effectively?

As programmers, we sometimes use a command with long options and parameters.
Later on, when we want to run that command again or modify it a bit, it would
be time-consuming to type it all over. I know that we can use `Ctrl-R` to
search the command history. However, its features are limited.

I find [fzf](https://github.com/junegunn/fzf) works better for this task, which
supports fuzzy-search your command history. To install fzf, run the following
command:

```zsh
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
# say yes to its install prompts
~/.fzf/install
```

Restart the shell, you should get a far better experience searching your
history commands (the shortcut is still `Ctrl-R`). To navigate the search
results, use `Ctrl-N` and `Ctrl-P`.

# Replace part of last command and re-execute

In Bash, we can use `fc -s old=new` to replace string `old` in the last command
executed to `new` and execute it again. Bash builtin `fc` is different from zsh
`fc`.

For `fc` in zsh, there is no `-s` option. The zsh equivalent is `fc -e -`. `-e`
options can be found in `man zshbuiltins`.

Zsh also provides `r` command which is same as `fc -e -`. You do not need to
define an alias for that any more.

Ref:

+ [Alternative to fc -s in zsh](https://superuser.com/q/794307/736190).
+ [zshbuiltins man page](https://linux.die.net/man/1/zshbuiltins).
