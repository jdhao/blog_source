---
title: "Switch Command with update-alternatives on Ubuntu"
date: 2020-07-02T23:06:40+08:00
tags: [Ubuntu]
categories: [Linux]
---

To build different projects, we may need to install different versions of the
same software. To use the specific version, we may create a sym link to the
executable with that version. On Ubuntu, we can also switch command versions
easily with [update-alternatives](http://manpages.ubuntu.com/manpages/trusty/man8/update-alternatives.8.html).

<!--more-->

One of the most common tool that need multiple versions is GCC. On Ubuntu
18.04, the default GCC version is 7.4.0. However, sometimes, we also need to
use older GCC such as gcc-4.8. The GCC toolset will install several executables
under `/usr/bin/`, such as `gcc-4.8`, `g++-4.8`, among others.

If we want the command `gcc` to point to `/usr/bin/gcc-4.8`, we often want
`g++` to point to `/usr/bin/g++-4.8`. Using symlinks to manage version switch
is not convenient in this case since we need to create two symlinks. If we want
to switch back to GCC 7.4.0, we then need to remove the old symlinks and create
new ones, which is cumbersome.

`update-alternatives` provides what is called `master` and `slave`
alternatives, where if you change the master symlinks, the slave symlinks will
also be changed automatically for you. The syntax is:

```bash
update-alternatives --install link name path priority [--slave link name path]
```

`link` is the generic name used to invoke a command, for example,
`/usr/bin/gcc`. `name` is the alternative name used under the alternative
directory (which is usually `/etc/alternatives`), for example, `gcc`. `path` is
the actual path to a specific version of command, for example,
`/usr/bin/gcc-4.8`. `priority` is a score given this alternative. If you have
several alternatives for a command, the one with the highest priority will be
used. The link after `--install` is the master link.

The format for `--slave` is the same as master link. You can add multiple slave
links like this:

```bash
update-alternatives --install link name path priority --slave link1 name1 path1 --slave link2 name2 path2
```

For GCC, we can use the following command to add two alternatives:

```
update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 10 --slave /usr/bin/g++ g++ /usr/bin/g++-4.8
update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 20 --slave /usr/bin/g++ g++ /usr/bin/g++-7
```

So by default, GCC 7.4 will be used. In order to switch GCC versions, we can use
the following command:

```bash
update-alternatives --config gcc
```

You can will be prompted to choose an alternative from a list of alternatives.
After that, the gcc and g++ version will be switched to the version you want.

The alternatives work like this. `/usr/bin/gcc` will point to
`/etc/alternatives/gcc`, which in turn points to the real command
`/usr/bin/gcc-7` or `/usr/bin/gcc-4.8` depending on which alternative you
choose. So when you change the alternative, you are changing the mapping from
`/etc/alternatives/gcc` to the actual executables.

# References

+ [how to use “update-alternatives” to manage multiple installed version of the same app?](https://askubuntu.com/q/529687/768311)
+ [How to choose the default gcc and g++ version?](https://askubuntu.com/q/26498/768311)
+ https://bitmingw.com/2019/08/28/ubuntu-update-alternatives/
