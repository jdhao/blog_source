---
title: "Docker 学习系列 --- 2"
date: 2020-05-02 16:18:30+0800
tags: [Docker]
categories: [Note]
---

本文总结一些 Docker 使用以及 Docker 镜像构建过程中遇到的问题。

<!--more-->

# Unable to find xxx locally

使用 `docker run -it centos:7` 命令，遇到提示：

> Unable to find image 'centos:7' locally

可以等待一会，收到提示：

```
7: Pulling from library/centos
Digest: sha256:307835c385f656ec2e2fec602cf093224173c51119bbebd602c53c3653a3d6eb
Status: Downloaded newer image for centos:7
```

当使用 `docker run` 运行镜像的时候，如果镜像不存在，那么 docker 会从 docker hub 寻找对应的镜像，下载运行。出现这个错误提示可能是因为网络原因，耐心等待就可以了，参考[这里](https://stackoverflow.com/q/48212796/6064933)

# `wget` 命令未找到

docker hub 上的 CentOS 镜像和普通的 CentOS 安装包有一定区别，执行的是最小安装，有很多常用的工具并没有安装，例如 `wget`, `which` 等，需要自己去安装。

Nvidia cuda 的 CentOS 系统镜像是基于 Docker hub 上 CentOS 7 的镜像，所以这些工具也没有安装，需要自己安装。

## 参考

+ https://stackoverflow.com/q/28885137/6064933

# docker run -it 选项作用

docker run 命令很常用的两个选项是 `-it`，`-i` 作用是把 host 的 input 和容器的 input 连接起来，`-t` 创造一个 pseudo terminal，这两个命令连起来使用，就好像直接在 container 里面创建了 bash shell，可以接收输入和输出，就像用户使用 host 机器的 shell 一样。

## 参考

+ https://stackoverflow.com/q/30137135/6064933
+ https://stackoverflow.com/a/22287905/6064933

# 如何从容器中拷贝东西到 host 机器

参考 [这里](https://stackoverflow.com/a/22050116/6064933)，可以使用 `docker cp` 命令：

```
docker cp CONTAINER_ID:/path/inside/docker/container /path/in/host/machine
```

# Dockerfile 里面的 RUN 命令默认运行目录在哪里？

Dockerfile 如果没有指定 [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir)，那么默认执行命令的目录是在 `/`，并不是 `/root`, 如果基础镜像指定了 work dir，那么 workdir 就是基础镜像指定的目录。因此在使用 Dockerfile 构建自己的镜像时，最好使用 `WORKDIR` 命令指定自己的工作目录，避免受到基础镜像的影响。

## 参考

+ [What's the default WORKDIR in docker](https://stackoverflow.com/a/49903714/6064933)?
+ https://stackoverflow.com/q/35286990/6064933

# Dockerfile 如何设置变量以便后续使用

使用 Dockerfile 构建镜像时，有时候我们需要设置一些变量，以便后续引用，节省时间。可以利用 [ARG](https://docs.docker.com/engine/reference/builder/#arg) 命令设置变量，指的注意的是，ARG 命令设置变量，只在 build 的时候有效，[ENV](https://docs.docker.com/engine/reference/builder/#env) 设置的变量，镜像 build 完成，进入镜像以后，仍然是有效的。

## 参考

+ [How to define a variable inside Dockerfile](https://stackoverflow.com/a/37639938/6064933).
+ [Difference between ARG and ENV](https://stackoverflow.com/a/41919137/6064933).

# COPY failed: Forbidden path outside the build context #

当我试图从当前 Dockerfile 所在目录的上一级目录拷贝某个文件到镜像的某个目录：

```
COPY ../some_file /path/inside/container
```

然后遇到了上述错误，原因是 docker 只允许从当前 Dockerfile 的[build context](https://medium.com/lucjuggery/docker-tips-about-the-build-context-dbc76505e178)下某个目录 copy 文件。

## 参考

+ [The purpose of build contex](https://stackoverflow.com/q/44465703/6064933)
+ https://medium.com/lucjuggery/docker-tips-about-the-build-context-dbc76505e178

# Dockerfile ARG 和 ENV 指令无法扩展某些变量或者符号

Dockerfile 的 ARG 和 ENV 指令，无法 expand `$HOME`, `~`，如果要使用路径，最好使用绝对路径。

例如，ctags 被安装在 /root/tools/ctags，如果用 ENV 定义 PATH 环境变量：

```
ENV PATH=$HOME/tools/ctags/bin:$PATH
```

然后在 Dockerfile 中运行下面的指令：

```
RUN which ctags
```

会提示错误，如果我们把 PATH 打印出来 `RUN echo $PATH`, 会发现 ctags 有关的一项目录是错的，显示的是 `/tools/ctags/bin`，也就是使用 ENV 指令定义 PATH 的时候，`$HOME` 是空的，并不会被 expand 为 `/root`。同样如果使用 `~`：

```
ENV PATH=~/tools/ctags/bin:$PATH
```

也不会被正确 expand 为 `/root`, 如果要运行 ctags 相关的指令也是不成功的。如果要设置环境变量，可以设置为绝对路径；如果觉得啰嗦，可以利用 ARG 自定义一个 HOME 变量：

```
ARG HOME=/root
```

然后在设置 ENV 变量的时候引用 HOME 变量就不会出错。

# 如何从某一个命令之后开始强制重新 build？

Dockerfile 如果从开始到某一步在 build 的时候没有出错，并且没有增加或者删除 build 的步骤，那么下一次 build 镜像的时候，默认使用上一步 build 的缓存，不会重新 build，这样可以加快镜像构建的速度。如果想从某一步开始重新开始 build，可以在这一步之前加入一个没用的不耗时的命令，例如

```
RUN echo "hello world"
```

重新构建的时候，新加入的命令及以后的所有步骤就会重新 build。

## 参考

+ [Disable cache for specific RUN command](https://stackoverflow.com/q/35134713/6064933).

# `source` 命令未找到

当我试着用 RUN 指令运行 `source` 命令:

```
RUN source /root/.bash_profile
```

遇到了错误提示：

> source: command not found

原因是什么呢，原来是 docker 默认用来执行命令的 shell 是 `/bin/sh`，并不是 bash，在 sh 下，`source` 命令并不存在，如果想要执行 `source` 命令。有两个办法，一个办法是使用 `SHELL` 指令，把使用的 shell 更改为 bash：

```
SHELL ["/bin/bash", "-c"]
```

另外一种办法是使用 [exec 形式的 RUN 指令](https://docs.docker.com/engine/reference/builder/#run)，仅仅在这个指令中使用 bash shell 而不是 sh：

```
RUN ["/bin/bash", "-c", "source /root/.bash_profile"]
```

## 参考

+ https://stackoverflow.com/a/39777387/6064933

# 登陆 container 以后 bash 不是 login shell

运行容器以后，发现 `/root/.bash_profile` 没有生效，经过排查，发现原因是 bash 不是 login shell，可以设置 CMD 指令，把 bash 变为 login shell：

```
CMD ["/bin/bash", "-l"]
```

# 不要使用 docker commit 方式创建镜像

启动 docker 镜像以后，如果在里面又进行了修改（例如安装了额外的工具或者进行了其他的配置）, 那么使用 `docker commit` 会保存这些更改，生成新的镜像。但是这种方式生成的镜像，除了 作者，其他用户很难清楚到底进行了什么操作，安装了哪些东西，不利于后续的维护，因此更好的方式是使用 Dockerfile 管理镜像，把要安装的软件都放到 Dockerfile 里面配置。这样的话，其他用户也可以轻松使用你的 Dockerfile 构建出同样的 Docker 镜像。

## 参考

+ https://yeasy.gitbooks.io/docker_practice/image/commit.html
+ https://stackoverflow.com/a/51763737/6064933

[^1]: 执行最小安装的原因是为了减少最终构建形成的镜像的大小，便于快速部署与下载运行。
