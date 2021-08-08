---
title: "Install Python3 in Ubuntu Docker"
date: 2021-01-17T01:41:06+08:00
draft: false
tags: [Docker, Ubuntu]
categories: [Note]
---

Build a Ubuntu docker with Python3 and pip support.

<!--more-->

I am using the official [Ubuntu docker](https://hub.docker.com/_/ubuntu). The
following is a minimum Dockerfile:

```docker
FROM ubuntu:18.04

RUN apt-get update && apt-get install -y software-properties-common gcc && \
    add-apt-repository -y ppa:deadsnakes/ppa

RUN apt-get update && apt-get install -y python3.6 python3-distutils python3-pip python3-apt
```

In order to install the more recent version of Python3, we use [ppa from
deadnakes](https://launchpad.net/~deadsnakes/+archive/ubuntu/ppa). It has
various Python versions from 3.1 to 3.10 for your need.

The command `add-apt-repository` is provided by package
`software-properties-common`. It can be used to add a custom package
repository. The `-y` option for `add-apt-repository` will say yes to all the
prompts, like what `-y` for `apt-get`does.

We need to install gcc because some python packages requires building when
installing via pip.

After adding the [`ppa:deadsnakes/ppa`](https://launchpad.net/~deadsnakes/+archive/ubuntu/ppa) repo,
pip3 is not included by default. Initially, I tried to install pip via [get-pip.py](https://pip.pypa.io/en/stable/installing/#installing-with-get-pip-py) manually,
and I get the error that [No module named 'distutils.util'](https://askubuntu.com/q/1239829/768311).
We need to install `python3-pip`, `python3-distutils` and `python3-apt` to
install pip3. This is the easiest way I found.


Ref:

+ https://phoenixnap.com/kb/how-to-install-python-3-ubuntu
