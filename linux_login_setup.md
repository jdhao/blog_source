---
title: "Why don't settings inside bashrc or bash_profile take effect?"
date: 2019-09-29 21:40:28+0800
tags: [shell, Bash]
categories: [Linux]
---

In this post, I would like to share causes and solutions to a few issues
related to login shell.

<!--more-->

# How to change default shell to Bash

When I logged into a new server, I found that somehow my default shell (`echo
$SHELL`) is `/bin/sh` instead of `/bin/bash`. We can change the default shell
with `chsh` command like this:

```bash
chsh -s /bin/bash
```

Make sure that the shell you want to switch are set in `/etc/shells`. Otherwise
you will fail.

# Settings in `bashrc` or `bash_profile` do not take effect after login

Another issue is that when I sshed to the server, I found that only settings
inside `.bash_profile` is sourced. Settings inside `.bashrc` didn't take
effect. Before we delve into this problem, we first need to understand the
basic concept of a login shell and an interactive shell.

## Login and interactive shell

### login shell ###

A login shell is usually the shell when the user first log in to the
system. If the user start another shell or use `bash` inside the current
shell, then the user will probably start a non-login shell[^1]. To
check whether the bash shell is a login shell, use the following
command:

```bash
# only works for bash
shopt -q login_shell && echo 'Login shell' || echo 'Not login shell'
```

### Interactive shell ###

Roughly speaking, an interactive shell is a shell that the user can interact
with via the terminal, i.e., typing a command, sending it to the shell and
getting output. If you run a script `test.sh` inside the shell by using `bash
test.sh`, what actually happens is that a non-interactive bash shell is started
to execute the commands inside `test.sh`. Now, you get a sense of what
*interactive* means. To check if a bash shell is interactive, you can see if
`i` is present in the variable `$-`:

``` bash
echo $-
# output may be: himBHs
```

### Different types of shell ###

According to the above statements, we now have four different types of a shell:

  - interactive login shell
  - interactive non-login shell
  - non-interactive login shell
  - non-interactive non-login shell

In the following table, I show how you can get these shells:

|           | interactive                                   | non-interactive                                |
| --------- | --------------------------------------------- | ---------------------------------------------- |
| login     | when you log into a server normally           | `ssh xxx@ip < test.sh`                          |
| non-login | start a new bash shell inside the login shell | when you execute script inside the login shell |

Non-interactive login shell is extremely rare. You can start one when
you [run a local script via ssh on remote.](https://stackoverflow.com/q/40853547/6064933):

```bash
ssh xxx@ip < test.sh
```

To verify that we have started a non-interactive login shell, put the
following the command inside `test.sh`:

```bash
echo $-
shopt -q login_shell && echo 'Login shell' || echo 'Not login shell'
```

The output of remote server will be:

```
hBs
Login shell
```

## Why isn't `.bashrc` sourced?

After looking up the bash manual, the section on [startup file](https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Bash-Startup-Files)
explains this behaviour:

> When Bash is invoked as an interactive login shell, or as a
> non-interactive shell with the –login option, it first reads and
> executes commands from the file /etc/profile, if that file exists.
> After reading that file, it looks for \~/.bash\_profile,
> \~/.bash\_login, and \~/.profile, in that order, and reads and
> executes commands from the first one that exists and is readable. The
> –noprofile option may be used when the shell is started to inhibit
> this behavior.

According to the above description, when you log into the server (bash is
interactive login shell), bash will first source `/etc/profile`, then it will
source `~/.bash_profile`, `~/.bash_login` and `~/.profile` in the order given.
The first one that exists and is readable is sourced. So `~/.bashrc` is not
sourced during the login process.

If you need to source `.bashrc` during login process, you can add the
following setting to `.bash_profile`:

``` bash
if [[ -f "$HOME/.bashrc" ]]; then
    source $HOME/.bashrc
fi
```

## Why isn't `.bash_profile` sourced?

Another strange case I met with a new server is that `~/.bash_profile` is not
executed during login. A little background here: for this destination server, I
need to log into a [jump server](https://en.wikipedia.org/wiki/Jump_server),
and choose the destination server. All users are initially logged in to the
destination server with the same user name. Then each user will have to switch
to his/her own account via `su` command: `su REAL_USERNAME`.

That is cause of the problem. The shell started by mere `su REAL_USERNAME` is
an interactive **non-login** shell. The bash manual also says that:

> When an interactive shell that is not a login shell is started, Bash
> reads and executes commands from \~/.bashrc, if that file exists. This
> may be inhibited by using the –norc option. The –rcfile file option
> will force Bash to read and execute commands from file instead of
> \~/.bashrc.

It means that only settings inside `$HOME/.bashrc` is sourced. The solution to
this issue is simple: [invoke `su` command with `-` or `-l` option](https://unix.stackexchange.com/q/7013/221410)
to tell Bash that we want a login shell instead of a non-login shell.

## What gets sourced when we run a non-interactive non-login shell?

The Bash manual says that for a non-interactive shell, it has the following behavior:

```
When bash is started non-interactively, to run a shell script, for example, it looks for the variable BASH_ENV
in  the  environment, expands its value if it appears there, and uses the expanded value as the name of a file
to read and execute.  Bash behaves as if the following command were executed:
       if [ -n "$BASH_ENV" ]; then . "$BASH_ENV"; fi
but the value of the PATH variable is not used to search for the filename.
```

So only the file represented by `BASH_ENV` is sourced when a non-interactive
shell is started.

# References

- [This post has good graph on what is sourced during bash startup](https://blog.flowblok.id.au/2013-02/shell-startup-scripts.html).
- [Bashrc isn’t always run when I log in](https://askubuntu.com/q/596538/768311).
- [Change shell in Ubuntu](https://unix.stackexchange.com/q/271858/221410).
- [How to get non-interactive login shell](https://unix.stackexchange.com/a/398105/221410).
- [How to check if a shell’s type: login or interactive](https://unix.stackexchange.com/q/26676/221410).
- [Difference between login, non-login, interactive and non-interactive shells](https://askubuntu.com/q/879364/768311).
- [Zsh doc on login and interactive shells](http://zsh.sourceforge.net/Guide/zshguide02.html#l7).
- [Difference between bashrc and bash\_profile](https://superuser.com/q/183870/736190).

[^1]: That is not always true. For example, for macOS, new terminal started by Terminal.app is actually a login shell, see [here](https://unix.stackexchange.com/q/119627/221410).
