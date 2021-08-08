---
title: "How to Install zplug inside Docker Container"
date: 2019-10-01 00:01:25+0800
tags: [shell, Docker]
categories: [Linux]
---

In the past few days, I tried to dockerize my development environment and put
my daily programming tools inside
[container](https://www.docker.com/resources/what-container). I tried to
install zplug inside docker container and met some issues. In this post, I want
to share how to install [zplug](https://github.com/zplug/zplug) and other
plugins inside the Docker container.

<!--more-->

Following the recommended way in the zplug git repo, I use the instruction
below to install zplug:

```
RUN curl -sL --proto-redir -all,https https://raw.githubusercontent.com/zplug/installer/master/installer.zsh | zsh
```

Unfortunately, the build fails and gives the following error message:

> The command '/bin/sh -c curl -sL --proto-redir -all,https https://raw.githubusercontent.com/zplug/installer/master/installer.zsh | zsh' returned a non-zero code: 1

After some searching, I find that this issue is caused by the Unicode
characters that zplug uses in its message. In order to deal with this, we need
to set up a locale in UTF-8 format (see
[here](https://jdhao.github.io/2019/09/27/linux_locale_settings/#set-up-locale-for-docker-container)
for details):

```
RUN apt-get install -y locales && locale-gen en_US.UTF-8
ENV LC_ALL en_US.UTF-8
```

Another issue is that I get an error when I use the following instruction to
install plugins:

```
RUN zplug install
```

The error message is that:

```
zplug: command not found
```

The command `zplug install` works in a normal terminal, but fails to work when
we are building a docker image. I change the instruction to

```
RUN ["zsh", "-ic", "zplug install"]
```

based on discussions [here](https://github.com/zplug/zplug/issues/272) and I
can run the command without errors.

Unfortunately, another error occurs when zplug tries to install plugins. The
errors look like this:

```
✘  Unknown error         lukechilds/zsh-nvm
✘  Unknown error         zsh-users/zsh-completions
✘  Unknown error         junegunn/fzf-bin
```

According to discussions [here](https://github.com/zplug/zplug/issues/359), it
is caused by the missing package `gawk`. We need to install the `gawk` package
before running the `zplug install` command:

```
RUN apt-get install gawk
RUN ["zsh", "-ic", "zplug install"]
```

After solving all these issues, I can install zplug and other plugins
successfully inside the docker container.
