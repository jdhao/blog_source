---
title: "How To Install Latest Version of Git on Linux"
date: 2021-03-27T21:48:34+08:00
draft: false
tags: [Git]
categories: [Git]
---

<details>
<summary><font size="3" color="red">Update log</font></summary>

<font color="blue">2021-12-10: update endpoint rpm link for git</font>
</details>

The version of the default git bundled with the Linux system is often too old,
and lacks certain features, which may [break some tools](https://github.com/lewis6991/gitsigns.nvim/issues/103).
This post summarizes how to install newer versions of Git on Linux.

<!--more-->

# Install via package manager

## CentOS

If you have root rights, you can install new version of git via yum.

```bash
# https://ius.io/setup
yum install \
https://repo.ius.io/ius-release-el7.rpm \
https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

yum remove git
yum install git224
```

The version of git installed is 2.24.4.

The following also work:

```bash
yum -y install https://packages.endpointdev.com/rhel/7/os/x86_64/endpoint-repo.x86_64.rpm
yum remove git
yum install git
```

# Compile and install git from source

First, we need to install some dependency packages. Note that if we use [https
protocol](https://gist.github.com/grawity/4392747) for git, we need to install
curl development package:

```
yum install libcurl-devel
```

Otherwise, when you git clone using a https url: `git clone https://xxx.xx.xx`,
you get an error:

> git: 'remote-https' is not a git command. See 'git --help'.

If you do not have root rights, you can also [build curl from source](https://curl.se/docs/install.html).

Then, run the following script to build git from source:

```bash
wget https://github.com/git/git/archive/refs/tags/v2.31.1.tar.gz -O git.tar.gz
tar xvf git.tar.gz
cd git-*
mkdir -p $HOME/local
make configure
./configure --prefix=$HOME/local
make -j && make install
```

`git` will be installed under `$HOME/local/bin`. Then, add `$HOME/local/bin` to
your `PATH`:

```bash
export PATH="$HOME/local/bin:$PATH"
```

Run `git --version` to check the git version.

Ref:

+ Install git via yum:
    + https://serverfault.com/q/709433/435975
+ Install git from source:
    + https://github.com/git/git/blob/master/INSTALL
    + https://www.digitalocean.com/community/tutorials/how-to-install-git-on-centos-7
    + [git: 'remote-https' is not a git command?](https://stackoverflow.com/a/65876436/6064933)
