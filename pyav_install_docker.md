---
title: "Install pyav inside Ubuntu Docker"
date: 2022-02-11T20:48:46+08:00
draft: false
tags: [ffmpeg, pyav, Docker, Ubuntu]
categories: [Note]
---

Building a usable Docker image with all the packages living peacefully is hard.
Here is how to install [pyav](https://github.com/PyAV-Org/PyAV) using Docker.

<!--more-->

Environment:

- system: Ubuntu 18.04
- python: 3.6.9
- pip: 20.3

# change apt source

First, a not-necessary but huge time saver is to change the apt source used.
Here in China, the official debian apt source is really slow.
We can change it to [aliyun](https://developer.aliyun.com/mirror/ubuntu) or [tsinghua](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/). Their access speed is quite high in China.

Here is the relevant snippet to change apt resource.

```docker
ARG APT_FILE=/etc/apt/sources.list

# change apt repo source
RUN mv $APT_FILE $APT_FILE.bak && \
  touch $APT_FILE && \
  echo  "deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse" >> $APT_FILE && \
  echo "deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse" >> $APT_FILE && \
  echo "deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse" >> $APT_FILE && \
  echo "deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse" >> $APT_FILE && \
  echo "deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse" >> $APT_FILE && \
  echo "deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse" >> $APT_FILE && \
  echo "deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse" >> $APT_FILE && \
  echo "deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse" >> $APT_FILE && \
  echo "deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse" >> $APT_FILE && \
  echo "deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse" >> $APT_FILE
```

# Install dependency packages

Second, we need to install necessary packages:

```docker
RUN apt-get update && apt-get install -y python-dev pkg-config
```

Note that installation instructions given in pyav doc [here](https://pyav.org/docs/develop/overview/installation.html#ubuntu-18-04-lts) does not work.
It seems that the installed version of libavformat and other libs are too old.
If we use that, we will see error message like this (also reported [here](https://github.com/aiortc/aiortc/issues/326) and [here](https://stackoverflow.com/q/66235459/6064933)):

```
building 'av.codec.codec' extension
  creating build/temp.linux-x86_64-3.6/src/av/codec
  x86_64-linux-gnu-gcc -pthread -DNDEBUG -g -fwrapv -O2 -Wall -g -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2 -fPIC -I/usr/include/python3.6m -Iinclude -I/usr/include/x86_64-linux-gnu -Ibuild/temp.linux-x86_64-3.6/include -I/usr/include/python3.6m -Ibuild/temp.linux-x86_64-3.6/include -c src/av/codec/codec.c -o build/temp.linux-x86_64-3.6/src/av/codec/codec.o
  src/av/codec/codec.c: In function ‘__pyx_pymod_exec_codec’:
  src/av/codec/codec.c:6061:36: error: ‘AV_CODEC_CAP_HARDWARE’ undeclared (first use in this function); did you mean ‘AV_CODEC_CAP_DR1’?
     __pyx_t_7 = __Pyx_PyInt_From_int(AV_CODEC_CAP_HARDWARE); if (unlikely(!__pyx_t_7)) __PYX_ERR(0, 124, __pyx_L1_error)
                                      ^~~~~~~~~~~~~~~~~~~~~
                                      AV_CODEC_CAP_DR1
  src/av/codec/codec.c:6061:36: note: each undeclared identifier is reported only once for each function it appears in
  src/av/codec/codec.c:6082:36: error: ‘AV_CODEC_CAP_HYBRID’ undeclared (first use in this function); did you mean ‘AV_CODEC_CAP_DR1’?
     __pyx_t_7 = __Pyx_PyInt_From_int(AV_CODEC_CAP_HYBRID); if (unlikely(!__pyx_t_7)) __PYX_ERR(0, 128, __pyx_L1_error)
                                      ^~~~~~~~~~~~~~~~~~~
                                      AV_CODEC_CAP_DR1
  error: command 'x86_64-linux-gnu-gcc' failed with exit status 1

  ----------------------------------------
  Failed building wheel for av
  Running setup.py clean for av
Failed to build av
```

## Add apt source for libav

Instead, we need to install the [ffmpeg-4 apt source](https://launchpad.net/~jonathonf/+archive/ubuntu/ffmpeg-4):

```
# add a new ffmpeg source, the builtin is too old
RUN apt-get update && apt-get install -y software-properties-common &&\
    add-apt-repository -y ppa:jonathonf/ffmpeg-4
```

After that, install the dependency lib for pyav:

```
RUN apt-get update && apt-get install -y \
    ffmpeg \
    libavformat-dev \
    libavcodec-dev \
    libavdevice-dev \
    libavutil-dev \
    libswscale-dev \
    libswresample-dev \
    libavfilter-dev
```

Finally, use pip to install pyav, it should be fine to install:

```
RUN pip install  -i https://pypi.tuna.tsinghua.edu.cn/simple av
```

# Ref

+ Official installation guide: https://pyav.org/docs/develop/overview/installation.html#installation
