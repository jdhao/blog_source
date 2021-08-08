---
title: "Set Up Lisp Dev Environment in Neovim"
date: 2020-11-08T23:43:20+08:00
tags: [Lisp]
categories: [Nvim]
---

A quick summary on how to set up Lisp Development environment in Neovim.

<!--more-->

First we need to install a Lisp distribution. [sbcl](http://www.sbcl.org/) is a
good start.

# Install sbcl

## Install using package manager

On Linux, we can install sbcl using package manager:

```bash
sudo apt update && sudo apt install sbcl

# or use the following for CentOS
# yum install sbcl
```

## Install binary release

We can also install a binary release if we do not have root right. All binary
releases can be found [here](https://sourceforge.net/projects/sbcl/files/sbcl/).
One caveat is that if you install the latest version of sbcl and run it, it
may not run due to dependency issues.

For example, on my system (CentOS 7.4), running `sbcl` fails since it requires
a newer libc.so. It complains that:

> sbcl: /lib64/libc.so.6: version `GLIBC_2.28' not found (required by sbcl)

The following steps are tested on CentOS 7.4 and works as expected.

```bash
wget https://master.dl.sourceforge.net/project/sbcl/sbcl/1.4.14/sbcl-1.4.14-x86-64-linux-binary.tar.bz2
tar xvf sbcl-1.4.14-x86-64-linux
cd sbcl-1.4.14-x86-64-linux
INSTALL_ROOT=$HOME/tools/sbcl sh install.sh
```

Also update PATH and SBCL_HOME env variable

```bash
export PATH="$HOME/tools/sbcl/bin:$PATH"
export SBCL_HOME="$HOME/tools/sbcl/lib/sbcl"
```

# Install quicklisp

[Quicklisp](https://www.quicklisp.org/beta/index.html) is a package manager for
Lisp. Here is how to install it.

On the command line:

```bash
curl -O https://beta.quicklisp.org/quicklisp.lisp
sbcl --load quicklisp.lisp
```

Then inside REPL, run:

```lisp
(quicklisp-quickstart:install :path "~/.quicklisp")
```

This will install quicklisp under `~/.quicklisp`.

Also inside REPL, run:

```lisp
(ql:add-to-init-file)
```

This will load quicklisp every time you start your Lisp.

# Fix arrow key issues

By default, inside sbcl, we can not use up and down arrow keys to navigate the
history. There is also no support for tab completion. We can use the package
[linedit](https://github.com/sharplispers/linedit) to fix this.

Inside REPL, run `(ql:quickload "linedit")` to install this package. Add the
following settings to `~/.sbclrc` to load linedit by default when starting sbcl:

```lisp
;;; Check for --no-linedit command-line option.
(if (member "--no-linedit" sb-ext:*posix-argv* :test 'equal)
    (setf sb-ext:*posix-argv*
      (remove "--no-linedit" sb-ext:*posix-argv* :test 'equal))
    (when (interactive-stream-p *terminal-io*)
      (require :sb-aclrepl)
      (require :linedit)
      (funcall (intern "INSTALL-REPL" :linedit) :wrap-current t)))
```

Ref:

https://common-lisp.net/project/linedit/

# Install vlime

Finally, install the Vim plugin [vlime](https://github.com/vlime/vlime) using vim-plug:

```vim
Plug 'vlime/vlime', {'rtp': 'vim/'}
```

Then run `sbcl sbcl --load path/to/vlime_plugin/vlime/lisp/start-vlime.lisp` to
start the vlime server. The actual path depends on where you install your
plugins.

Some handy shortcuts provided by Vlime:

+ `\cc`: create a server connection.
+ `\cs`: choose a server connection.
+ `\ss`: send the current line to REPL for evaluation.
+ `\i`: toggle interactive mode (in interactive mode, press `<CR>` will execute the code)

# References

+ http://www.sbcl.org/getting.html
+ quicklisp
    + https://www.quicklisp.org/beta/
    + https://lispcookbook.github.io/cl-cookbook/getting-started.html
