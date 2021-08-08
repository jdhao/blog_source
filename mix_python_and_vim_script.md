---
title: "How to Use Python Inside Vim Script with Neovim"
date: 2019-04-22 16:05:10+0800
tags: [Python]
categories: [Nvim]
---

# Introduction

I have been using [Vim-airline](https://github.com/vim-airline/vim-airline) for
a while to customize my statusline. We can change the theme used for statusline
with the many themes available in
[vim-airline-themes](https://github.com/vim-airline/vim-airline-themes). Not
all these themes looks good since it depends on the background color you use
for Neovim and certainly your aesthetics.

<!--more-->

I only use a handful of themes myself and I change my themes when I feel bored
with one theme. It occurs to me that I can write a simple script to randomly
pick a theme from my favorite list[^1]. It turns out writing such a script is not
easy for Neovim newbies like me.

In this post, I would like to share how I end up achieving what I want by
mixing Vim script and Python script.

# Prerequisite

This post is specifically targeted at Neovim users so that some of the settings
may not apply if you use Vim.

First, you need to install Python and Python 3 is preferable since [Python 2
support will stop in 2020](https://python3statement.org/). Make sure that you
can call `python` from the command line. When you start Neovim and `:echo
has('python3')` will print `1` in the command line.

You should also install [pynvim](https://github.com/neovim/pynvim), which is
the Python client and plugin host for Neovim. You can install it with pip:

```
pip install pynvim
```

Finally, you should set the variable `g:python3_host_prog` to the location of
Python executable. If your Python executable path is `~/anaconda3/bin/python`,
you should add the following setting in your `init.vim`:

```vim
let g:python3_host_prog=expand('~/anaconda3/bin/python')
```

After that, open Neovim and run `:checkhealth provider` and make sure there is
no error in the part about Python 3. A sample output is shown below:

```
## Python 3 provider (optional)
  - INFO: Using: g:python3_host_prog = "D:/Anaconda/python"
  - INFO: Executable: D:\Anaconda\python
  - INFO: Python version: 3.6.5
  - INFO: pynvim version: 0.3.2
  - OK: Latest pynvim is installed.
```

# Pick a random airline theme

## Generate a random number in the given range

To pick a random theme from my favorite themes, I need to create a function
which return a random index of the theme given the lowest and highest index.
First, I will show my final function and then I will explain it bit by bit.

<details>
<summary><font color="red">Click to see the function.</font></summary>

```vim
" generate a random integer from range [Low, High] using Python
function! RandInt(Low, High) abort
" if you use Python 3, the python block should start with `python3` instead of
" `python`, see https://github.com/neovim/neovim/issues/9927
python3 << EOF
import vim
import random

# using vim.eval to import variable outside Python script to python
idx = random.randint(int(vim.eval('a:Low')), int(vim.eval('a:High')))

# using vim.command to export variable inside Python script to vim script so
# we can return its value in vim script
vim.command("let index = {}".format(idx))
EOF

return index
endfunction
```
</details>


In the above function `RandInt()`, we define a function which accept two
parameter `Low` and `High`. We generate a random integer between in the range
$[Low, High]$ using the Python package
[`random`](https://docs.python.org/3/library/random.html) and return the index.

The format for Python script is as follows:

```
python[3] << MARKER
# python code here
MARKER
```

If you use Python 2, you should use `python` keyword, otherwise use `python3`,
which is explained [here](https://github.com/neovim/neovim/issues/9927). To
manipulate Neovim inside Python script, we need to import package `vim`.

To get the value of Vim variable `Low` and `High` inside Python script, we need
to *import* them. `vim.eval()`[^2] is used to get their value, which is used to
evaluate the expression using built-in expression evaluator. According to
documentation, the return value is:

> - a string if the Vim expression evaluates to a string or number

So we need to use `int()` to convert the return value to integer.

After we get the value of `idx`, how do we use it outside the Python script? We
can use `vim.command()` to achieve that:

```
vim.command("let index = {}".format(idx))
```

`vim.command(str)` will execute `str` as Vim command, thus assigning the value
of `idx` to Vim variable `index`.

## Pick a airline theme

Now we can define a list of themes to pick and generate a random index and set
the airline theme using the theme at the index. An example code is shown below:

```vim
let s:candidate_airlinetheme = ['alduin', 'ayu_mirage', 'base16_flat',
    \ 'monochrome', 'base16_grayscale', 'lucius', 'base16_tomorrow',
    \ 'base16color', 'biogoo', 'distinguished', 'gruvbox', 'jellybeans',
    \ 'luna', 'raven', 'seagull', 'solarized_flood', 'term', 'vice', 'zenburn']

let s:idx = RandInt(0, len(s:candidate_airlinetheme)-1)
let s:theme = s:candidate_airlinetheme[s:idx]
let g:airline_theme=s:theme
```

In the above script, `len()` is used to get the length of a list.

# Conclusion

In this post, I introduced how to mix Python script with Vim script.
Specifically, I write a simple function to generate a random index and then
pick a airline theme based on the index. I have just scratched the surface of
the powerful ability of Python programming inside Vim. For more information,
see `:h python`.

# References

+ [Hack vim with Python](https://jvanz.com/hacking-your-vim-with-python.html).
+ [python or python3 in script](https://github.com/neovim/neovim/issues/9927).
+ [Export value from Python to Vim](https://stackoverflow.com/q/17656320/6064933).
+ [Can I script vim using Python](https://vi.stackexchange.com/questions/9/can-i-script-vim-using-python).
+ [Import vim variable to Python script](https://vi.stackexchange.com/q/8045/15292).

[^1]: Note that vim-airline provides a command [`AirlineTheme random`](https://github.com/vim-airline/vim-airline/commit/148eb6bb2860e77f384d205abb06a2f24534e8ac) to pick a random theme, but you cannot set the list of color themes you want to use.
[^2]: See `:h python-eval` for detailed documentation.
