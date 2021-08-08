---
title: "Install Clangd on CentOS 7"
date: 2021-07-03T00:51:34+08:00
draft: false
tags: [Clang]
categories: [Linux]
---

Clangd is a language server for C++/C etc. On  CentOS 7, I try to use clangd 12
for code auto-completion. Clangd can be easily installed via [its binary
release](https://github.com/clangd/clangd/releases/tag/12.0.0).

<!--more-->

# My failed attempt to use clang binary release

When I run `clangd --version`, I see the following error:

> clangd_12.0.0/bin/clangd: /lib64/libc.so.6: version `GLIBC_2.18' not found (required by clangd_12.0.0/bin/clangd)

The reason is that the shared library `libc.so` is too old for clangd to run.
We need to use a newer version of glibc (at least glibc-2.18).

I tried to  clone glibc and build version 2.20 from scratch:

```bash
git clone https://mirrors.tuna.tsinghua.edu.cn/git/glibc.git
cd glibc
git checkout glibc-2.20

cd ..
# Note that glibc requires out-source building, i.e., we are forbidden to build glibc inside its source directory.
mkdir glibc-build
../glibc/configure --prefix=$HOME/local
make -j 16
make install
```

I add `$HOME/local/lib` to `LD_LIBRARY_PATH`. Then things go badly wrong. Every
executable I use segfaults immediately. For example, when I use `ls`,  it will
print out:

> zsh: segmentation fault (core dumped)  ls -F --color=auto

According to [this post](https://stackoverflow.com/q/39847912/6064933), this is
caused by version mismatch: the glibc version requried to run the executable is
different from what is provided right now.

I tried the glibc-6.18 release tar downloaed from [here](http://mirrors.ustc.edu.cn/gnu/libc/). The same errors happens when I add
`$HOME/local/lib` to `LD_LIBRARY_PATH` and run `clang`.

# Build clangd from source

Maybe we need to build llvm project to use clangd. Here is how to compile llvm
from source:

```bash
git clone --depth=1 https://github.com/llvm/llvm-project.git

cd llvm-project
mkdir build && cd build
cmake -G "Unix Makefiles" -DLLVM_ENABLE_PROJECTS="clang;clang-tools-extra" -DCMAKE_INSTALL_PREFIX=~/tools/llvm -DCMAKE_BUILD_TYPE=Release ../llvm
make -j 16
make install
```

Some explanation about the options used:

+ `-DLLVM_ENABLE_PROJECTS="clang;clang-tools-extra"`: it specifies which project we want to build. To use `clangd` and `clang-tidy`, `clang-tools-extra` is a must.
+ `-DCMAKE_INSTALL_PREFIX=~/tools/llvm`: it specifies where we want to install llvm.
+ `-DCMAKE_BUILD_TYPE=Release`: it specifies the build type. `Release` type is smaller than `Debug`.

For more detail on Cmake options when building llvm, check [here](https://llvm.org/docs/CMake.html).

Since llvm is a huge project, it may take a long time to build. It takes 37m to
build on my server with 16 concurrent processes.

This time, clangd works without errors. After installing llvm, do not forget to
add llvm binary to your `PATH` variable.

# How to use clangd

According to [clangd documentation](https://clangd.llvm.org/installation.html), you can either generate a `compile_commands.json`
file for your project or use `compile_flags.txt`.

For simple project, using `compile_flags.txt` is sufficient. Your compile flags
are written one per line. A sample `compile_flags.txt` looks like this:

```txt
-Wall
-std=c++11
-L/usr/local/lib
-I/usr/local/include
-lfmt
```

# References

+ glibc segfaults issue:
    + https://unix.stackexchange.com/q/272606/221410
    + https://stackoverflow.com/q/19709932/6064933
+ Build clangd: https://github.com/llvm/llvm-project/tree/main/clang-tools-extra/clangd#building-and-testing-clangd
