---
title: "Nifty Nvim/Vim Techniques That Make My Life Easier -- Series 10"
date: 2021-06-17T01:54:41+08:00
draft: false
tags: []
categories: [Nvim]
---

This is the 10th post of my post series on nifty Nvim/Vim techniques that will make my editing experience easier.

<details>
<summary><font size="3" color="red">Click here to check other posts in this series.</font></summary>

+ Series 11: https://jdhao.github.io/2021/11/22/nifty_nvim_techniques_s11/
+ Series 9: https://jdhao.github.io/2021/01/07/nifty_nvim_techniques_s9/
+ Series 8: https://jdhao.github.io/2020/11/11/nifty_nvim_techniques_s8/
+ Series 7: https://jdhao.github.io/2020/09/22/nifty_nvim_techniques_s7/
+ Series 6: https://jdhao.github.io/2019/12/21/nifty_nvim_techniques_s6/
+ Series 5: https://jdhao.github.io/2019/11/11/nifty_nvim_techniques_s5/
+ Series 4: https://jdhao.github.io/2019/09/17/nifty_nvim_techniques_s4/
+ Series 3: https://jdhao.github.io/2019/05/14/nifty_nvim_techniques_s3/
+ Series 2: https://jdhao.github.io/2019/04/17/nifty_nvim_techniques_s2/
+ Series 1: https://jdhao.github.io/2019/03/28/nifty_nvim_techniques_s1/
</details>

<!--more-->

# Equal sign (`=`) is recognized as part of file name

I have a config file where it has a line like `file_path=path/to/file.txt`.
When the cursor is after the `=` sign, I tried to use `gf` to open the file
`path/to/file.txt`. I got the following error:

> E447: Can't find file "file_path=path/to/file.txt" in path.

Apparently, Vim thinks that `=` is part of the file name and tries to open the
wrong file. It turns out that `gf` is using the option `isfname` to decide
which character is valid for a file name. So we need to remove `=` from this
option.

```vim
set isfname-==
```

# Open a buffer in vertical split

There are several ways to do this:

+ `:vsplit +b{count}`: `{count}` represents the buffer number, as shown by `:ls` or `:buffers` command.
+ `:vsplit #{count}`: `{count}` has the same meaning as above.
+ `:vert sb {count}`: `{count}` has the same meaning as above.
+ `:vert sb {buf_name}`:  `{buf_name}` represents the path to a file. It supports completion for buffer names, i.e., you only need to type part of the path and then press `<Tab>` for completion.

Ref:

+ https://vi.stackexchange.com/q/76/15292
+ https://vi.stackexchange.com/q/8122/15292

# Disable syntax highlighting for a particular filetype

When we open a YAML file using Neovim, if the file has thousands of lines,
cursor movement will be slowed down considerably due to Vim's slow syntax
highlighting feature. In this case, we may want to turn syntax highlight off
for YAML based on its line numbers. We can use `FileType` autocmd to detect the
file type and set the `syntax` option to disable syntax highlighting.

```vim
augroup yaml_syntax_off
   autocmd!
   autocmd FileType yaml if line('$') > 500 | setlocal syntax=OFF | endif
augroup END
```

Note that we use `setlocal syntax=OFF` instead of `syntax off`, because `syntax
off` will turn off syntax highlight for all the buffers currently opened, which
is not what we want.

Ref:

+ https://vi.stackexchange.com/q/298/15292
+ https://stackoverflow.com/q/51658852/6064933

# I get error when I try to call a plugin function in init.vim?

I want to call a plugin's function in my init.vim, but I get an error that the
function does not exist? However, when nvim is opened, I call the function
without any errors. What happened?

The called function is in a script under `plugin` directory.  This is because
the plugin has not been properly initialized. We can call the plugin function
after `VimEnter` event:

```vim
autocmd VimEnter * call my_plugin_func()
```

Ref:

+ https://vi.stackexchange.com/q/14907/15292
+ https://vi.stackexchange.com/q/23264/15292
+ https://groups.google.com/g/vim_use/c/i2HWD_9V-28?pli=1
+ https://github.com/wbthomason/packer.nvim/issues/4#issuecomment-662262954

# Generate a list of incrementing numbers one per line

I would like to generate a list of incrementing numbers:

```
1
2
3
4
5
```

How do we do it in Vim? First, generate a list of same numbers. Input 0 in the
first line, press `yy` and then press `4p` to generate five zeros:

```
0
0
0
0
0
```

Then move the cursor to first 0, and press `Ctrl-V`, followed by `4j` to visual
select this block. Finally, press `g`, followed by pressing `Ctrl-A`. We will
get what we want.

Another way is to use `:put` command: `:put =range(1, 5)`, which is more concise.

Ref:

+ https://vi.stackexchange.com/q/12/15292
