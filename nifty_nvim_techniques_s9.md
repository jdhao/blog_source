---
title: "Nifty Nvim/Vim Techniques That Make My Life Easier -- Series 9"
date: 2021-01-07 23:37:07+0800
tags: []
categories: [Nvim]
---

This post continues my [previous post](https://jdhao.github.io/2020/11/11/nifty_nvim_techniques_s8/)
on nifty Nvim/Vim techniques.

<!--more-->

# How do I return a key press from a function and use it in a mapping

I want to write a function to return `<Tab>` key or `<Ctrl-N>` based on whether
completion menu is available, and use the return value in an insert mode
mapping. The code is:

```vim
inoremap <expr> <Tab> MyTabFun()
function! MyTabFun()
  if pumvisible()
    return "<C-N>"
  else
    return "<Tab>"
  endif
endfunction
```

However, the function returns those characters literally instead of as key
press. This because Vim thinks that you want to insert those keys literally. To
mean a key press, we need to escaped it. Like the following:

```vim
return "\<C-N>"
" or
" return "\<Tab>"
```

The relevant vim doc on this topic is `:h expr-quote`.

Ref:

+ [Using a special key in function](https://vi.stackexchange.com/q/20206/15292)
+ [How to invoke the increment and decrement Normal-mode commands in Vimscript?](https://stackoverflow.com/q/6409509/6064933)
+ [Why do I need to use unicode for a control character in a mapping using <expr> and a function?](https://vi.stackexchange.com/q/6072/15292)

# Get character at a specific index in a multi-byte aware fashion

Unlike Python, in Vim script, string indexing uses byte index by default, not
character indexing. Bytes indexing works well for ASCII characters. Once your
string contains multi-byte characters, things no long works as expected. For
example, if we run the following code

```vim
let a = '你好吗'
echo a[0]
```

Vim prints `<e4>` not `你` , the first byte of `你` in UTF-8 encoding (the
binary representation for 你 using UTF-8 encoding is `\xe4\xbd\xa0`,
see [here](https://www.fileformat.info/info/unicode/char/4f60/index.htm)).

How to we get the character at a specific index? Using [`strcharpart()`](https://neovim.io/doc/user/eval.html#strcharpart()) instead.
For example,

```vim
let my_str = '你好吗'
"result will be '你', the first char in my_str
echo strcharpart(my_str, 0, 1)
" result will be '好', the second char in my_str
echo strcharpart(my_str, 1, 1)
```

We can also use the following convenience function:

```vim
function!  CharAtIdx(str,  idx)  abort
    " Get char at idx from str. Note that this is based on character index  " instead of the byte index.
    return  strcharpart(a:str,  a:idx,  1)
endfunction
```

Then, to get first char of a string, use `CharAtIdx(my_str, 0)`.

# Get string length regardless of ASCII or not

This is related to previous tips. There is function `strlen()` or `len()` in
Vim, but they only calculates byte length of a string, instead of character
length, like what `len()` function in Python does. We can instead use the
[`strchars()`](https://neovim.io/doc/user/eval.html#strchars()) function to get
string length. I consider this one of the many hidden quirks of Vim. We just
need to get accustomed to it.

# Use neovim as git diff and merge tool

Here is how to set up neovim as a git diff and git merge tool. Add the
following config to the file `$HOME/.gitconfig`:

```ini
[diff]
    tool = nvimdiff
[difftool]
    prompt = false
[difftool "nvimdiff"]
    cmd = "nvim -d \"$LOCAL\" \"$REMOTE\""
[merge]
    tool = nvimdiff
[mergetool]
    prompt = true
[mergetool "nvimdiff"]
    cmd = "nvim -d \"$LOCAL\" \"$REMOTE\" \"$MERGED\" -c 'wincmd w' -c 'wincmd J'"
```

Ref:

+ https://gist.github.com/huytd/78c676a797f81eb08cdedf5400e1b6a3
+ https://oozou.com/til/use-neovim-as-a-git-difftool-57
+ https://github.com/neovim/neovim/issues/2377#issuecomment-126092442

# Move the view horizontally

If we do not wrap the text and the line text length exceed the window size,
some text will be hidden beyond the current view port. To move the view port
horizontally, Vim has the following normal mode command:

+ `{count}zl`:  move the current view port {count} characters to the right, default is 1 if no {count} provided.
+ `{count}zh`: move the current view port {count} characters to the left, default is 1 if no {count} provided.
+ `zL`:  move the current view port half screen width to the right.
+ `zH`: move the current view port half screen width to the left.

The default behavior for `zL` and `zH` is to move the view port half
screen width, which may be too much. We can map these shortcuts to use smaller
steps:

```vim
nnoremap zL 10zl
nnoremap zH 10zh
```

Ref:

+ [Horizontal navigation in long lines](https://stackoverflow.com/q/5989739/6064933)
