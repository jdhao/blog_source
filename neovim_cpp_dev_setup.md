---
title: "Setting up Neovim for C++ Development with LSP"
date: 2020-11-29 23:55:52+0800
draft: false
tags: [C++, Clang]
categories: [Nvim]
---

In this post, I would like to share how to set up Neovim for writing simple C++
programs.

<!--more-->

# Prerequisite

First, we need to install additional packages or tools. The installation
sections below are mostly aimed at Linux. For macOS, clang is already installed,
and for ccls, you can simply install it via HomeBrew:

```
brew install ccls
```

## Install gcc

LLVM requires gcc to be at least 5.1, first, we need to update gcc.

### Install gcc-7 on CentOS 7

```bash
# How to do it on Ubuntu
sudo yum install centos-release-scl
sudo yum install devtoolset-7-gcc*
scl enable devtoolset-7 bash
which gcc
gcc --version
```

Ref:

+ https://stackoverflow.com/a/39731134/6064933

### Install gcc-7 on Ubuntu 16.04

Install a newer version of GCC since it is required to compile ccls[^1]. On
Ubuntu, you can install newer gcc via the following command:

```bash
sudo apt-get install -y software-properties-common
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt update
sudo apt install g++-7 -y
```

Set up gcc-7 to be the default:

```bash
update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 60 --slave /usr/bin/g++ g++ /usr/bin/g++-7
sudo update-alternatives --config gcc
```

Ref:

+ [Install gcc-7 on Ubuntu 16.04](https://gist.github.com/jlblancoc/99521194aba975286c80f93e47966dc5)

## Install CMake

Ccls requires newer version of cmake. The cmake on my system is tool old.
First, we need to download cmake binary release:

```bash
wget https://hub.fastgit.org/Kitware/CMake/releases/download/v3.20.1/cmake-3.20.1-linux-x86_64.sh
mkdir $HOME/tools/cmake
bash cmake-3.18.4-Linux-x86_64.sh --prefix=$HOME/tools/cmake --exclude-subdir --skip-license
```

Add cmake executable to PATH:

```bash
export PATH="$HOME/tools/cmake/bin:$PATH"
```

Ref:

+ [How do I install the latest version of cmake from the command line?](https://askubuntu.com/a/865294/768311)

## Install llvm and clang

To install clang, either build it from source or install the binary release if
it is available for your system.

### Build from source

Follow the guide [here](https://clang.llvm.org/get_started.html) on building Clang and LLVM on your platform.

```bash
git clone --depth=1 https://hub.fastgit.org/llvm/llvm-project.git
mkdir -p llvm-project/build
cd llvm-project/build
cmake -G "Unix Makefiles" -DLLVM_ENABLE_PROJECTS="clang;clang-tools-extra" -DCMAKE_INSTALL_PREFIX=~/tools/llvm -DCMAKE_BUILD_TYPE=Release ../llvm

# Note that make -j may error out due to excessive memory usage, so we restrict
# the number of processor used to compile llvm.
make -j 16
```

The option `-DCMAKE_INSTALL_PREFIX` specify where we want to install llvm and
clang. Since I do not have root rights, I install it under `$HOME/tools/llvm`.
You may change it to other directory. We add `clang-tools-extra` to option
`DLLVM_ENABLE_PROJECTS` so that extra tools like `clangd` and `clang-tidy` can
also be built.

### Install binary release

Note that for some systems, clang also has pre-built binary so you do not need
to build from source yourself, see [here](https://releases.llvm.org/download.html).

For example, there is binary release for Ubuntu 16.04:

```bash
wget https://github.com/llvm/llvm-project/releases/download/llvmorg-11.0.0/clang+llvm-11.0.0-x86_64-linux-gnu-ubuntu-16.04.tar.xz
tar xvf clang+llvm-11.0.0-x86_64-linux-gnu-ubuntu-16.04.tar.xz
```

<font color="red">Do not forget to add the binary and include path to your `PATH`
and `LD_LIBRARY_PATH` env variable.</font>

## ccls

[ccls](https://github.com/MaskRay/ccls) is a [Language Server](https://microsoft.github.io/language-server-protocol/) implementation for C++/C etc.
It can be used for C++ code completion, linting, formatting etc.
Ccls build instruction can be found [here](https://github.com/MaskRay/ccls/wiki/Build).

### Build ccls

With all its dependencies installed, we can now build ccls:

```bash
git clone --depth=1 --recursive https://github.com/MaskRay/ccls
cd ccls
cmake -H. -BRelease -DCMAKE_BUILD_TYPE=Release -DCMAKE_PREFIX_PATH=$HOME/tools/clang+llvm-11.0.0-x86_64-linux-gnu-ubuntu-16.04
cmake --build Release
```

If you see the following error:

> src/utils.hh:18:20: fatal error: optional: No such file or directory

This is because ccls can not find the `optional` header files. Your gcc version
is too old, you should install gcc-7. See above section on how to install gcc-7
on Ubuntu.

Another error I met is that `libtinfo` is not found when compiling:

> /usr/bin/ld: cannot find -ltinfo

Just install `libtinfo-dev` using apt:

```
sudo apt install libtinfo-dev
```

After all these steps, ccls should be compiled successfully. Add the executable
directory to your `PATH`.

Ref:

+ https://github.com/MaskRay/ccls/issues/41
+ https://github.com/MaskRay/ccls/issues/469
+ https://github.com/MaskRay/ccls/issues/195

# Neovim config

Now comes to the configuration for Neovim.

## Auto-completion

For auto-completion, we use [vim-lsp](https://github.com/prabirshrestha/vim-lsp), together with [deoplete-vim-lsp](https://github.com/lighttiger2505/deoplete-vim-lsp) and [deoplete](https://github.com/Shougo/deoplete.nvim).

An example config is shown below:

```vim
Plug 'prabirshrestha/vim-lsp'

Plug 'Shougo/deoplete.nvim'
Plug 'lighttiger2505/deoplete-vim-lsp'

" setting with vim-lsp
if executable('ccls')
   au User lsp_setup call lsp#register_server({
      \ 'name': 'ccls',
      \ 'cmd': {server_info->['ccls']},
      \ 'root_uri': {server_info->lsp#utils#path_to_uri(
      \   lsp#utils#find_nearest_parent_file_directory(
      \     lsp#utils#get_buffer_path(), ['.ccls', 'compile_commands.json', '.git/']))},
      \ 'initialization_options': {
      \   'highlight': { 'lsRanges' : v:true },
      \   'cache': {'directory': stdpath('cache') . '/ccls' },
      \ },
      \ 'whitelist': ['c', 'cpp', 'objc', 'objcpp', 'cc'],
      \ })
endif
```

If you are on Linux, the above config should work as expected. As soon as you
started editing C++ source files, code auto-completion for standard C++ header
and for methods/class in standard library should work.

However, if we only use the above config, auto-completion only works for
standard C++ libraries, since ccls does not know where to find the header file
for other packages we use. We can create a `.ccls` under the project root to
tell ccls our compilation flags. An example config for a source file using
[OpenCV](https://github.com/opencv/opencv) is like the following:

```txt
clang
%h -x c++-header
-Wall
-Wextra
%cpp -std=c++11
%c -std=c11
-I/home/jdhao/local/include/opencv4
-I.
```

On macOS, I have encountered completion issues even for standard libraries. It
seems that clang can not find the correct directory for the header files of
standard libraries. The following is a working `.ccls` file:

```txt
clang
-isystem/Library/Developer/CommandLineTools/usr/include/c++/v1
%cpp -std=c++11
```

The directory `/Library/Developer/CommandLineTools/usr/include/c++/v1` is where
macOS stores the standard C++ header files. It uses the system clang. If you
do not know where that directory is, use the following command:

```bash
clang -v -fsyntax-only -x c++ /dev/null
```

Some of the output will show the possible directories where standard C++ may
exist:

```txt
#include <...> search starts here:
 /usr/local/include
 /Library/Developer/CommandLineTools/usr/bin/../include/c++/v1
 /Library/Developer/CommandLineTools/usr/lib/clang/11.0.0/include
 /Library/Developer/CommandLineTools/usr/include
 /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include
 /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks (framework directory)
```

If you install ccls on macOS via HomeBrew, it will also install a separate
clang/llvm package on your system. The llvm directory is like
`/usr/local/Cellar/llvm/11.0.0`. If that is the case, the following `.ccls`
file is also working for macOS:

```txt
clang
-ccc-install-dir
/usr/local/Cellar/llvm/11.0.0/bin
%cpp -std=c++11
%cpp -stdlib=libc++
```

It uses clang installed via homebrew. The standard C++ header file location now
becomes `/usr/local/Cellar/llvm/11.0.0/include/c++/v1/`, also see [here](https://github.com/MaskRay/ccls/issues/160#issuecomment-449653569).

If your project is a CMake project, you can also generate a
`compile_commands.json` file for ccls to work. For the details, see [here](https://jdhao.github.io/2020/12/12/build_opencv_project_with_cmake/#generate-complilation-databases).

Ref:

+ ccls config:
    + ccls [Initialization options ](https://github.com/MaskRay/ccls/wiki/Customization#initialization-options) (can be set up via LS client in vim script)
    + https://github.com/MaskRay/ccls/wiki/Customization
    + https://github.com/MaskRay/ccls/wiki/Project-Setup#ccls-file
    + https://github.com/neovim/nvim-lspconfig/issues/70
+ ccls C++ header file completion
    + https://github.com/MaskRay/ccls/issues/191#issuecomment-453809905
    + https://github.com/MaskRay/ccls/issues/368
    + https://github.com/MaskRay/ccls/issues/450

## Syntax highlighting

If you do not want to use lsp-based highlight, you may try [chromatica.nvim](https://github.com/arakashic/chromatica.nvim) or
[vim-cpp-enhanced-highlight](https://github.com/octol/vim-cpp-enhanced-highlight). Vim-cpp-enhanced
highlight is base on regex matching to highlight symbols. It does not
understand the code. It may not be accurate compared to LSP.

For lsp highlight, use [vim-lsp-cxx-highlight](https://github.com/jackguo380/vim-lsp-cxx-highlight).
I can not make it work with nvim-lsp though. It works with vim-lsp.

Example config:

```vim
Plug 'jackguo380/vim-lsp-cxx-highlight'
```

## tags and navigation

We can use [gutentags](https://github.com/ludovicchabant/vim-gutentags) to generate tags for us. Example config below:

```vim
let  g:gutentags_ctags_tagfile = '.tags'
let  s:vim_tags = expand('~/.cache/tags')
let  g:gutentags_cache_dir = s:vim_tags
let  g:gutentags_ctags_extra_args = ['--fields=+niazS', '--extra=+q']
let  g:gutentags_ctags_extra_args += ['--c++-kinds=+px']
let  g:gutentags_ctags_extra_args += ['--c-kinds=+px']
```

## Compilation and run

If you are writing a large code project, you should use `make` or other [build
tools](https://en.wikipedia.org/wiki/List_of_build_automation_software). For simple programs, using clang directly is convenient.

This is how to compile and run a simple program via the built-in terminal:

```vim
nnoremap <silent> <buffer> <F9> :call <SID>compile_run_cpp()<CR>

function! s:compile_run_cpp() abort
  let src_path = expand('%:p:~')
  let src_noext = expand('%:p:~:r')
  " The building flags
  let _flag = '-Wall -Wextra -std=c++11 -O2'

  if executable('clang++')
    let prog = 'clang++'
  elseif executable('g++')
    let prog = 'g++'
  else
    echoerr 'No compiler found!'
  endif
  call s:create_term_buf('v', 80)
  execute printf('term %s %s %s -o %s && %s', prog, _flag, src_path, src_noext, src_noext)
  startinsert
endfunction

function s:create_term_buf(_type, size) abort
  set splitbelow
  set splitright
  if a:_type ==# 'v'
    vnew
  else
    new
  endif
  execute 'resize ' . a:size
endfunction
```

# References

+ http://www.skywind.me/blog/archives/2084

[^1]: Strictly speaking, you can build ccls via clang, but I find it hard and can not seem to make it work.
