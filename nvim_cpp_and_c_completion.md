---
title: "Set up for C++/C Development in Neovim"
date: 2020-04-19 18:17:56+0800
tags: [C++, C, Clang]
categories: [Nvim]
---

This post summarizes how to set up various plugins needed for C++/C code
editing, linting and formatting.

<!--more-->

# Pre-requisite

This post assumes that [deoplete](https://github.com/Shougo/deoplete.nvim) has
been installed and set up properly on your system. If that is not the case, you
may refer to [this post](https://jdhao.github.io/2019/06/06/nvim_deoplete_settings/)
on how to do it.

# Install clang and llvm

To enable C++/C code auto-completion, we need to install [clang](https://clang.llvm.org/) first.

## Linux

On Linux, we can build the latest clang from source using the following
command:

```bash
git clone --depth=1 https://github.com/llvm/llvm-project.git
cd llvm-project
mkdir build && cd build
cmake -DLLVM_ENABLE_PROJECTS=clang -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release  ../llvm
make -j$(nproc)
```

The flag `-DCMAKE_BUILD_TYPE=Release` will build a release version of clang,
which is much smaller than the default one (`Debug` type).

The build process will take about half of an hour on our 12-core server, and
the time may vary depending on your hardware.

After that, we need to add the clang binary path to the `PATH` variable, and
add the directory containing `libclang.so` to env variable `LD_LIBRARY_PATH`.
Edit `~/.bash_profile` and add following settings:

```bash
export PATH="$HOME/tools/llvm-project/build/bin:$PATH"
export LD_LIBRARY_PATH="$HOME/tools/llvm-project/build/lib:$LD_LIBRARY_PATH"
```

Under directory `$HOME/tools/llvm-project/build/bin`, there are a lot of
executables, including `clang`, `clang++`, and `clang-format` (which we will
use to format C++/C code later).

**Do not forget to source the file** to refresh the env variables.

## macOS

For macOS, clang is shipped with it so we do not need to install clang. We need
to install `clang-format` though since `clang-format` is not shipped with clang
by default (source [here](https://stackoverflow.com/a/21192263/6064933)).

The easiest way to install clang-format is via Homebrew:

```bash
brew install clang-format
```

On Mac, the name of the clang share library is `libclang.dylib`, and we should
add its parent directory to env variable `LD_LIBRARY_PATH`[^1]:

```bash
export LD_LIBRARY_PATH="/Library/Developer/CommandLineTools/usr/lib/:$LD_LIBRARY_PATH"
```

# Auto-completion

To provide C++/C code autocompletion, we can use
[deoplete-clang](https://github.com/deoplete-plugins/deoplete-clang):

```vim
" Install using vim-plug
Plug 'deoplete-plugins/deoplete-clang'
```

If you have set up the libclang path properly, then auto-completion should work
when you start editing C++/C source files.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/clang_autocompletion_cpp.jpg" width="800">
</p>

# Linting

For code linting, we can use [ale](https://github.com/dense-analysis/ale). Add
the following setting to Neovim config:

```vim
let g:ale_linters = {
    \ 'python': ['pylint'],
    \ 'vim': ['vint'],
    \ 'cpp': ['clang'],
    \ 'c': ['clang']
\}
```

# Code formatting

We can use the command line tool `clang-format` shipped with clang for code
formatting, with the help of pluign
[Neoformat](https://github.com/sbdchd/neoformat) to format our code. Make sure
that `clang-format` is on your `PATH`. Add the following settings to Neovim
config:

```vim
" custom setting for clangformat
let g:neoformat_cpp_clangformat = {
    \ 'exe': 'clang-format',
    \ 'args': ['--style="{IndentWidth: 4}"']
\}
let g:neoformat_enabled_cpp = ['clangformat']
let g:neoformat_enabled_c = ['clangformat']
```

Notice that the linter name for `clang-format` is `clangformat`, not
`clang-format`. I have wasted ten minutes trying to find why `clang-format`
does not with Neoformat until I found [here](https://github.com/sbdchd/neoformat/issues/234)
that we should use `clangformat` as the linter name.

# References

+ [Build and run clang -- official guide](https://clang.llvm.org/get_started.html).
+ [Build clang](https://shaharmike.com/cpp/build-clang/).
+ [ALE: supported tools](https://github.com/dense-analysis/ale/blob/master/supported-tools.md).
+ [clang-format doc](https://clang.llvm.org/docs/ClangFormat.html).
+ [clang-format style options](https://clang.llvm.org/docs/ClangFormatStyleOptions.html#).

[^1]: you can find its path via command `mdfind -name libclang.dylib`.
