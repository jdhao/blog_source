---
title: "Linux Tips and Tricks -- s3"
date: 2020-12-22T23:04:48+08:00
draft: false
tags: []
categories: []
---

# Calculate the stat of a column of numbers

Suppose we have a file where each line a number, and we want to the get stat of
these numbers on the command line.

## Use python

If Python is installed in the system, we can use the following command:

```bash
cat data.log | python3 -c "import fileinput as FI,statistics as STAT; i = [float(l.strip()) for l in FI.input()]; print('min:', min(i), ' max: ', max(i), ' avg: ', STAT.mean(i), ' median: ', STAT.median(i))"
```

## Use jq

We can also use [jq](https://stedolan.github.io/jq/) to calculate the stat:

+ min: `jq -s min data.txt`
+ max: `jq -s max data.txt`
+ average: `jq -s add/length data.txt`

Ref:

+ [Is there a way to get the min, max, median, and average of a list of numbers in a single command?](https://unix.stackexchange.com/q/13731/221410)

# What if I screw up the PATH variable?

I edit the `.bash_profile`and update the `PATH` variable wrongly, something
like this:

```bash
export PATH="/some/path"
```

After sourcing this file, all my commands fails. This is because the system
relies on `PATH` variable to find the suitable executable to run.

However, we can always run an executable with its absolute path, so the remedy
is to use vim to edit `.bash_profile`:

```bash
/usr/bin/vim ~/.bash_profile
```

Ref:

+ https://apple.stackexchange.com/q/192657/285595

# Search pattern only in package names

```bash
apt search --names-only pattern
```

`pattern` supports regular expressions. For example, to search package starting
with a `man`:

```bash
apt search --name ^man
```

Ref:

+ [apt search: limit to exact match](https://askubuntu.com/q/934739/768311)

# I was forced log out after a short time of inactivity?

I connect to a remote server via my ssh client. I noticed that after even a few
minutes of inactivity, I was forced log out, and saw the following message:

>  timed out waiting for input: auto-logout

It turns out bash a feature to log out the user using the `TMOUT` env variable.
For example, `TMOUT=3600` will force user to log out after waiting 3600 seconds
for user input (but no input is coming). Set `TMOUT` to 0 will disable this
feature.

Ref:

+ https://www.linvirtshell.com/2017/11/changing-auto-logout-timeout-in-ssh.html
+ [How to keep idle session from exiting](https://unix.stackexchange.com/q/340642/221410)

# Find which package provides a file or what file a package contains on Ubuntu

To find which package provides a certain file, we can use `dpkg` command:

```bash
dpkg -S /path/to/file
```

For example `dpkg -S /usr/lib/python3/dist-packages/uno.py` shows the following
message:

> python3-uno: /usr/lib/python3/dist-packages/uno.py

We can also use apt-file to find which package a file belongs to:

```bash
apt update && apt instal apt-file
apt-file update
apt-file search /path/to/file
```

To find what file a package contains, we can to this [Ubuntu packages site](https://packages.ubuntu.com/)
and search a package. In the package detail info page, we can click the `[list
of files]`  to get the files contained in a certain release for an architecture
(see [here](https://packages.ubuntu.com/en/bionic/amd64/python3-uno/filelist) for an example).


Ref:

+ [How do I find the package that provides a file?](https://askubuntu.com/q/481/768311)
+ [Find what package a file belongs to in Ubuntu/Debian?](https://superuser.com/q/10997/736190)
