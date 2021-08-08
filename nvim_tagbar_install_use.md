---
title: "How to Use Tagbar in Neovim"
date: 2018-09-28T12:17:03+08:00
tags: [tags]
categories: [Nvim]
---

If you have a source file with hundreds or thousands of lines of code. How to
you see its structure and go to some classes or methods quickly in Nvim? The
solution is to use [tagbar](https://github.com/majutsushi/tagbar).

<!--more-->

# Installation

To use tagbar, you have to install
[universal-ctags](https://github.com/universal-ctags/ctags), which will
generate tag files for tagbar to use.

## Install universal-ctags

### For Linux

We need to build and install by ourself:

```bash
git clone https://github.com/universal-ctags/ctags.git
cd ctags
./autogen.sh
./configure --prefix=/where/you/want/to/install # install to where you have access
make -j && make install # may require extra privileges depending on where to install
```

Under the install directory, there are two directories: `bin` 和 `share`. The
ctags executable is in the `bin` directory. We need to add this `bin` directory
to the system `PATH` variable:

```bash
# suppose that you have install ctags to $HOME/tools/ctags
echo export PATH=$HOME/tools/ctags/bin:$PATH >> ~/.bash_profile
source ~/.bash_profile
```

### For Mac OS

For Mac OS, if you have installed [Homebrew](https://brew.sh/), you can simply
using the following command to install ctags:

```bash
brew install --HEAD universal-ctags/universal-ctags/universal-ctags
```

Homebrew will do everything for you. No need to set up install path.

## Install tagbar

Then install tagbar with your favorite plugin manager such as vim-plug:

```
Plug 'majutsushi/tagbar'
```

Use `:PlugInstall` to install tagbar.

# Using tagbar

Open your code and use `:TarbarToggle` to toggle the tagbar window. You should
be able to see the tagbar window with all your classes, methods and variables.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/89581661.jpg" width="400">
</p>

In the above image, the window on the right is the tagbar window.

If you frequently use tagbar, you should consider adding a shortcut for this
command like the following:

```vim
nnoremap <silent> <C-K><C-T> :TagbarToggle<CR>
```

Tagbar also provides some shortcut for tag operation. Place the cursor on some
tags in the tagbar window:

+ `<Enter>`:  go to the line in the code where the tag occur, the cursor will be in the source code
+ `p`: Like the `<Enter>` key, except that the cursor is still in tagbar window
+ `q`: quit the tagbar window

# References

+ [Install ctags on Linux](https://github.com/universal-ctags/ctags/blob/master/docs/autotools.rst).
+ [Install ctags on Mac OS](https://github.com/universal-ctags/homebrew-universal-ctags).
