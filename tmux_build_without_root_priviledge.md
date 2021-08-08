---
title: "Building and Install Tmux from Source on Linux"
date: 2018-10-16 13:21:00 +08:00
tags: [Tmux]
categories: [Linux]
---

Tmux is a great terminal multiplexer. Tmux installed via the package manager is
often too old. So I decided to install the latest version of Tmux that supports
true colors from source. Since I do not have root privilege on the system, I
have to build Tmux and install it under my `HOME`. If that is also what you
want, keep reading.

<!--more-->

Tmux has two dependencies: [libevent](https://github.com/libevent/libevent) and
[ncurses](https://invisible-island.net/ncurses/). We have to build and install
these two libraries first.

# Build libevent

First, we use the following script to build and install libevent:

```bash
wget https://github.com/libevent/libevent/releases/download/release-2.1.11-stable/libevent-2.1.11-stable.tar.gz
tar zxvf libevent-2.1.11-stable.tar.gz
cd libevent-2.1.11-stable
mkdir -p $HOME/local
# install libevent under $HOME/local
./configure --prefix="$HOME/local"
make -j && make install
```

Libevent will be install to `$HOME/local`.

# Build ncurses

We also need to build ncurses library:

```bash
wget https://ftp.gnu.org/pub/gnu/ncurses/ncurses-6.2.tar.gz
tar zxvf ncurses-6.2.tar.gz
cd ncurses-6.2/
./configure --prefix="$HOME/local"
make -j&& make install
```
The above command will install ncurses under `$HOME/local`.

# Build Tmux

Finally, we download the latest Tmux release tar ball from GitHub and build it:

```bash
wget https://github.com/tmux/tmux/releases/download/3.1b/tmux-3.1b.tar.gz
tar zxvf tmux-3.1b.tar.gz
cd tmux-3.1b
# the CPPFLAGS and LDFLAGS are important, make sure you have written them correctly, or the build will fail
./configure --prefix=$HOME/local \
    CPPFLAGS="-I$HOME/local/include -I$HOME/local/include/ncurses" \
    LDFLAGS="-L$HOME/local/lib"
make -j && make install
```

After running the above commands, Tmux will be installed to `$HOME/local`. The
`tmux` executable is under `$HOME/local/bin`.

## Post install

We need to add Tmux executable to env variable PATH.

```bash
export PATH="$HOME/local/bin:$PATH"
```

After building Tmux, we should also update the env variable `LD_LIBRARY_PATH`
to include the libevent and libncurses shared object directory:

```bash
export LD_LIBRARY_PATH="$HOME/local/lib:$LD_LIBRARY_PATH"
```

Or you may meet the following error when starting tmux:

> tmux: error while loading shared libraries: libevent-2.1.so.7: cannot open
> shared object file: No such file or directory

Now, you can use the latest Tmux and configure it as you wish. You may also be
interested in the following posts:

+ [configure tmux to support true color](https://jdhao.github.io/2018/10/19/tmux_nvim_true_color/).
+ [Tmux questions and trouble shootings](https://jdhao.github.io/2018/10/23/tmux_questions_and_trouble_shooting/).
+ [Vim like settings for Tmux](https://jdhao.github.io/2018/09/30/tmux_settings_for_vim_users/).
+ [Tmux install and management](https://jdhao.github.io/2019/01/17/tmux_plugin_management/).

The complete script to build Tmux are shown below.

<details>
<summary><font size="2" color="red">Click to show the code.</font></summary>

```bash
# Build libevent
wget https://github.com/libevent/libevent/releases/download/release-2.1.11-stable/libevent-2.1.11-stable.tar.gz
tar zxvf libevent-2.1.11-stable.tar.gz
cd libevent-2.1.11-stable
mkdir -p $HOME/local
# install libevent under $HOME/local
./configure --prefix="$HOME/local"
make -j && make install
cd ..

# Build ncurses
wget https://ftp.gnu.org/pub/gnu/ncurses/ncurses-6.2.tar.gz
tar zxvf ncurses-6.2.tar.gz
cd ncurses-6.2/
./configure --prefix="$HOME/local"
make -j&& make install
cd ..

# Build Tmux
wget https://github.com/tmux/tmux/releases/download/3.1b/tmux-3.1b.tar.gz
tar zxvf tmux-3.1b.tar.gz
cd tmux-3.1b
# the CPPFLAGS and LDFLAGS are important, make sure you have written them correctly, or the build will fail
./configure --prefix=$HOME/local \
    CPPFLAGS="-I$HOME/local/include -I$HOME/local/include/ncurses" \
    LDFLAGS="-L$HOME/local/lib"
make -j && make install
```
</details>

# References

+ [Build libevent](https://github.com/tmux/tmux/issues/1084).
+ [Build Tmux from source](https://gist.github.com/ryin/3106801).
+ Tmux readme: https://github.com/tmux/tmux
