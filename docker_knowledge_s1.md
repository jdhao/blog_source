---
title: "Docker 学习系列 --- 1"
date: 2020-05-02 15:23:59+0800
tags: [Docker]
categories: [Note]
---

Docker 是一种类似虚拟机的机制，允许我们在 host 机器创建一个隔绝的小型操作系统环境，并且可以保存下这个环境（称为 Docker 镜像），把这个 Docker 镜像部署到其他 host 机器，各个机器上的环境完全一致。这样我们就不需要每次在部署新环境的时候，重复执行安装程序、配置环境的流程，大大简化了我们的工作，提升了工作效率。

<!--more-->

# Docker 镜像的使用

Docker 镜像就像一个小的操作系统，从哪里获取我们需要的 Docker 镜像呢？ [Docker hub](https://hub.docker.com/) 是 Docker 官方提供的镜像托管仓库，里面包含了很多用户上传的镜像。docker hub 上的镜像，可以直接使用 `docker run` 命令来运行镜像，不需要 `docker pull`,然后再 `docker run`。如果运行一个镜像不在本地，docker 会尝试从 docker hub 下载这个镜像，然后再运行。

## `镜像` 与 `容器` 两个概念有什么区别？

可以简单认为，镜像是死的，是冻结的容器，镜像实例化运行以后，就是容器，所以从一个镜像可以运行多个容器，这些容器初始状态都是完全一样的。镜像和容器的关系，类似于编程中 ”类“ 和 ”对象“ 的关系。

### 参考 ###

+ https://stackoverflow.com/q/21498832/6064933
+ https://stackoverflow.com/q/23735149/6064933

## 如何退出当前运行的 docker 容器

如果以 `-t` 方式运行容器，那么可以使用 `ctrl-p` 然后 `ctrl-q` 挂起容器，后续可以再使用 attach 命令进入容器，参考[这里](https://stackoverflow.com/a/44347530/6064933)。

## 重新进入正在运行的容器？

当容器以交互式方式运行 (`-i`) 时，可以在多个地方连接到这个容器，查看容器的内容，连接到正在运行的容器的命令是：

```
docker attach CONTAINER_ID
```

此时在一个地方输入指令，其他地方也会同时显示相同的指令和输出结果。

## 列出系统上正在运行的运行

要列出系统上目前正在运行的容器：

```
docker ps
```

## 停止容器运行

要停止一个正在运行的 container，使用

```
docker stop CONTAINER_ID
```

container 运行退出以后，不会自动删除，还在系统上，需要重启的时候可以使用(参考 [这里](https://stackoverflow.com/a/39689438/6064933))

```
docker start -ai CONTAINER_ID
```

## 删除容器

如果需要删除某个 container，可以使用(参考[这里](https://docs.docker.com/engine/reference/commandline/rm/))

```
docker rm CONTAINER_ID
```

# Cuda Dockerfile 资源 #

英伟达官方提供了大量的含有 Cuda 环境的 Dockerfile 资源，地址如下：

> https://gitlab.com/nvidia/container-images/cuda

包含了各个系统以及各个版本的 cuda。如果要构建使用英伟达显卡的镜像，可以使用这些镜像中的一个作为基础镜像。

# Docker 镜像构建重要心得 #

我们可以使用 Dockerfile 来构建 Docker 镜像，Dockerfile 相当于是构建镜像的模版。构建镜像最令人烦躁的问题就是在构建过程中突然出错，我们不得不一次次修改 Dockerfile，解决各种问题。所以构建 Docker 镜像的一个重要心得就是：尽量防止 build 出错。

我们构建镜像的时候，一般会从一个基础镜像开始构建，由于基础镜像通常执行的是最小安装[^1]，很多常用工具并没有安装，所以镜像 build 过程中，经常因为没有安装一些库而中途出错。一个好的方法是开两个 terminal 窗口，一个窗口，可以先 docker run 进入基础镜像的容器，然后在容器里面一步一步运行想要执行的操作，如果在基础镜像里面运行没有错误，在另外一个 terminal 窗口，把这些操作写到 Dockerfile；如果在基础镜像里面运行某个命令有错误，那么解决这个错误，把需要添加的库或者需要执行的命令补充到 Dockerfile 里面。

按照上面这个步骤来建立 Dockerfile，可以极大减少出错的机会。

