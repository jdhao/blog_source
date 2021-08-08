---
title: "Why Cannot I Get the Value of A Env Variable in Python?"
date: 2020-07-15 22:17:07+0800
tags: [Bash]
categories: [Linux]
---

I found a strange issue when I wanted to get the value of an env variable from
Python script.

<!--more-->

To reproduce, we can first set the value of `FOO` in the Bash shell:

```Bash
$ FOO=123
```

Then execute the following python command:

```Bash
$ python -c "import os; print(os.getenv('FOO', "None"))"
```

The output is `None`. So the python process can not see the variable `FOO`.
However, if we run the two command in one line:

```Bash
$ FOO=123 python -c "import os; print(os.getenv('FOO', "None"))"
```

The output will be `123`.

If we use `export FOO=123` instead of `FOO=123`, we can also get the value
of `FOO` in Python.

But why? What is the difference here? This is in fact a bit complexer than what
I think.

# Bash builtin and external commands

First, we need to know that, when Bash executes commands, it treats the builtin
and external commands differently. Builtin commands are those that are,
literally, built into the Bash executable. External commands are those commands
that are not part of the Bash shell.

When Bash executes builtin commands, it does not create a new process. When it
executes external commands, it will create a new shell and then execute the
external commands. The new shell will inherit the environment variables of its
parent shell (see [here](https://askubuntu.com/q/428458/768311) on why Bash
needs to create a new process to execute an external command).

How do we check if a command is a Bash builtin or an external command?

First, we can check the output of `help`, it will list all Bash builtin
commands. We can also use the `type` command to check if a command is a builtin
command:

```Bash
type -t some_command
```

For builtin commands, it will output `builtin`. For external commands, it will
output different info, for example, `file` (see `help type` for more info).

Ref:

+ https://www.theunixschool.com/2012/03/internal-vs-external-commands.html
+ [Check if a command is builtin](https://stackoverflow.com/q/7399713/6064933).
+ [How does shell execute commands](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_01_03.html).
+ https://linuxconfig.org/internal-vs-external-linux-shell-commands

# When will variable value be available in a process?

If we define variable without using `export`, it is not part of the environment
variable. When Bash create a new process, this variable will not be available
there.

One exception is that if you set the variable and run the command in the same
line, it will be available only to that command. From the [Bash
manual](https://www.gnu.org/software/Bash/manual/html_node/Environment.html#Environment):

> The environment for any simple command or function may be augmented
> temporarily by prefixing it with parameter assignments, as described in
> [Shell Parameters](https://www.gnu.org/software/Bash/manual/html_node/Shell-Parameters.html#Shell-Parameters).
> These assignment statements affect only the environment seen by that command.

If a variable is not exported, it is only a shell variable. When it is
exported, it is an environment variable that will be available in all
subprocesses.

Ref:

+ [Defining a variable with or without export](https://stackoverflow.com/q/12561654/6064933).
+ [Bash: difference between “export k=1” vs. “k=1”](https://stackoverflow.com/q/12561654/6064933).
+ [Difference between shell variables which are exported and those which are not in bash](https://unix.stackexchange.com/q/3507/221410)
+ [Difference between “a=b” and “export a=b” in bash](https://superuser.com/q/18988/736190)

# Back to the issue

Now with sufficient background knowledge on how Bash works, we are able to
understand why certain ways work or fail.

```Bash
# Fails
$ FOO=123
$ python -c "import os; print(os.getenv('FOO', "None"))"
```

The above way to get the value of `FOO` fails, because `python` is an external
command for Bash. Bash will execute `python` in a new process. However, `FOO`
defined via `FOO=123` is **only** available in the shell that invoking `python`
command, not the sub-process (also a Bash shell) that actually run `python`
command.

```Bash
# work
$ FOO=123 python -c "import os; print(os.getenv('FOO', "None"))"
```

The above way works because the variable definition is in the same line with
`python` command. It will be available temporily for this command. If you
execute `python -c "import os; print(os.getenv('FOO', "None"))` in a second
line, it will not work any more.

```Bash
$ export FOO=123
$ python -c "import os; print(os.getenv('FOO', "None"))"
```

This way also works because we set an environment variable `FOO` via `export`.
So `FOO` will be available to all subprocesses created by this shell.

# References

+ [python - os.getenv and os.environ don't see environment variables of my Bash shell](https://stackoverflow.com/q/19070615/6064933).
+ [Why can't Python see environment variables](https://stackoverflow.com/q/31993885/6064933).
