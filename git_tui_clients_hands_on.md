---
title: "Hands on with Three Git TUI Clients"
date: 2021-09-11T00:33:12+08:00
draft: false
tags: []
categories: [Git]
---

If you are not accustomed to typing git commands on the command line, there are
also some TUI clients for easing the use of git.

<!--more-->

# lazygit

[lazygit](https://github.com/jesseduffield/lazygit) is a git tui client written in go. To install it on Linux:

```bash
wget https://github.com/jesseduffield/lazygit/releases/download/v0.28.2/lazygit_0.28.2_Linux_x86_64.tar.gz

mkdir -p $HOME/tools/lazygit
tar --directory=$HOME/tools/lazygit -xvf lazygit_0.28.2_Linux_x86_64.tar.gz
```

Add it to PATH:

```bash
export PATH=$HOME/tools/lazygit:$PATH
```

Lazygit comes with rich features and extensive key bindings.

# gitui

[gitui](https://github.com/extrawurst/gitui) is a similar git tui written in
rust, which boasts excellent performance against other git tui clients. Install
it on Linux:

```bash
wget https://github.com/extrawurst/gitui/releases/download/v0.16.2/gitui-linux-musl.tar.gz
mkdir -p $HOME/local/bin
tar --directory=$HOME/local/bin -xvf gitui-linux-musl.tar.gz

export PATH=$HOME/loca/bin:$PATH
```

gitui also has rich features. One issue to note is that it does not support
using `j` and `k` to select next and previous item, which is a bit clusmy for
Vim users like me.

# tig

[tig](https://github.com/jonas/tig) is tiny tools written in C, which provides
a git tui with basic features. We can install it from source:

```
wget https://github.com/jonas/tig/releases/download/tig-2.5.4/tig-2.5.4.tar.gz
tar xvf tig-2.5.4.tar.gz
cd tig-2.5.4
./configure --prefix=$HOME/local
make -j 8 && make install
```

tig will be installed under `$HOME/local/bin`.

Tig is tiny and quite powerful and suitable for simple use cases.
