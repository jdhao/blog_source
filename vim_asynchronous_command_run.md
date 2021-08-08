---
title: "Running Command Asynchronously inside Neovim"
date: 2019-12-09 23:27:10+0800
tags: [Vim]
categories: [Nvim]
---

# Introduction

When I am writing some prototyping code inside Neovim, I want to run the script
without going to the terminal and typing `python toy_script.py`. Of course, we
can use `:!python %` in the command line to run the script (see `:h :!`). The
problem is that running external command will block Nvim/Vim, and we can not
move the cursor until the script finishes running.

<!--more-->

Of course, on Linux and Mac, you can use `!python % &` to run the script in the
background, but you can not get the output of the script.

#  Asynchronous command in Nvim/Vim

Fortunately, both [Neovim](https://neovim.io/doc/user/job_control.html) and [Vim](https://vimhelp.org/channel.txt.html#job-start) provides the job feature to run external command asynchronously. However, to use those features, you have to write non-trivial amount of code. Thanks to plugins like [vim-dispatch](https://github.com/tpope/vim-dispatch) and [asyncrun.vim](https://github.com/skywind3000/asyncrun.vim), we can use this feature easily.

# Asyncrun.vim

In this post, I would like to share how to set up asyncrun.vim and use it. First, you can install it with vim-plug:

```
Plug 'skywind3000/asyncrun.vim'
```

It provides the `AsyncRun` command to run external shell commands asynchronously. For example, to run the current Python script, you can use the following command:

```
" use quote to avoid spaces in file names
:AsyncRun python "%"
```

The above command will run current script in the background without blocking Neovim. You can still edit the source code as you will when the program is running..

Asyncrun.vim will put the output of the command in the quickfix list. To see the command output, use `:copen` to open the quickfix window. You can also set up a config to open the quickfix window automatically when you use `AsyncRun`. Just add the following setting to your `init.vim` or vimrc:

```vim
let g:asyncrun_open = 6
```

Then a quickfix window of 6 line tall will be opened automatically after asyncrun starts.

# Python related issues

## Command output encoding

When you use asyncrun.vim on Windows, you may find that Chinese characters may not be displayed properly in the quickfix window. That is because the command output encoding on Windows may not be the same with the encoding you use (suppose you are using `utf-8` encoding). You can set up the variable `g:asyncrun_encs` to `gbk` encoding to solve this issue:

```vim
if has('win32')
    " Command output encoding for Windows
    let g:asyncrun_encs = 'gbk'
endif
```

Then Chinese characters should display properly.

## Display command output in real time

When python script is running in background, the output is buffered, i.e., you can not see the script output immediately after a `print()` statement is executed. You will only see the output after cache buffer is full or when the script finishes execution. There are two easy ways to fix this issue.

The first is to add `PYTHONUNBUFFERED` variable in your `init.vim` or vimrc:

```vim
let PYTHONUNBUFFERED=1
```

Or you need to run python command with `-u` option:

```vim
:AsyncRun python -u "%"
```

We can also create a shortcut to execute script quickly:

```vim
augroup python_file
    autocmd!
    autocmd FileType python nnoremap <F9> :AsyncRun python -u "%"<CR>
augroup END
```

# References

+ [Execute shell command silently in Vim.](https://vi.stackexchange.com/questions/1942/how-to-execute-shell-commands-silently)
+ [Disable output buffering for Python.](https://stackoverflow.com/q/107705/6064933)
+ [Can not see python script output in real time.](https://github.com/skywind3000/asyncrun.vim/wiki/FAQ#cant-see-the-realtime-output-when-running-a-python-script)
