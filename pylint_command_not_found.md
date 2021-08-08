---
title: "Pylint: command not found?"
date: 2019-11-28 01:21:54+0800
tags: [pylint]
categories: [Python]
---

The system I am currently using has Anaconda pre-installed by root under system
locations.

<!--more-->

To install user packages, we need to install them to a user location. For
example, to install pylint, we need to add `--user` option for `pip install`:

```
pip install --user pylint
```

However, when I use `pylint` command, I get the following error:

> -bash: pylint command not found

Since there is no error when installing pylint, I think there is no problem
with installation. I use the `find` command and find that pylint executable is
placed under `~/.local/bin`, which is not part of the PATH variable.
So the reason is that `pylint` executable is installed into a directory that
isn't in your PATH.

A more reliable way to find where the pylint executable is installed is to list
the files which are installed in pylint with the `pip show` command:

```
pip show -f pylint
```

You will see part of the output like the following:

```
Location: /home/xxxx/.local/lib/python3.6/site-packages
Requires: astroid, mccabe, isort
Required-by:
Files:
  ../../../bin/epylint
  ../../../bin/pylint
  ../../../bin/pyreverse
  ../../../bin/symilar
```

So the location of pylint executable is `$HOME/.local/bin/`. You should add the
directory to system PATH to use pylint:

```bash
export PATH=$HOME/.local/bin:$PATH
```

For [flake8](https://github.com/PyCQA/flake8), the same issue exists and the solution is the same, which I
will not elaborate on.

# References

+ [Pip show which files are installed with a package.](https://pip.pypa.io/en/stable/reference/pip_show/)
+ https://stackoverflow.com/q/51358987/6064933
+ https://stackoverflow.com/q/48015106/6064933
+ [flake8 not found.](https://stackoverflow.com/questions/45956359/zsh-command-not-found-flake8-but-flake8-is-installed)

