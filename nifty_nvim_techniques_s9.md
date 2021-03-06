---
title: "Nifty Nvim/Vim Techniques That Make My Life Easier -- Series 9"
date: 2021-01-07 23:37:07+0800
tags: [diff]
categories: [Nvim]
---

This is the 9th post of my post series on nifty Nvim/Vim techniques that will make my editing experience easier.

<details>
<summary><font size="3" color="red">Click here to check other posts in this series.</font></summary>

+ Series 11: https://jdhao.github.io/2021/11/22/nifty_nvim_techniques_s11/
+ Series 10: https://jdhao.github.io/2021/06/17/nifty_nvim_techniques_s10/
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

# How do I return a key press from a function and use it in a mapping

I want to write a function to return `<Tab>` key or `<Ctrl-N>` based on whether
completion menu is available, and use the return value in an insert mode
mapping. The initial code is:

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
press. This is because Vim thinks that you want to insert those keys literally.
To signal a key press, we need to escaped it. Like the following:

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
character indexing. Byte indexing works well for ASCII characters. Once your
string contains multi-byte characters, things no long works as expected. For
example, if we run the following code

```vim
let a = '?????????'
echo a[0]
```

Vim prints `<e4>`, the first byte of [`???` in UTF-8 encoding](https://www.fileformat.info/info/unicode/char/4f60/index.htm) (the binary
representation for ??? using UTF-8 encoding is `\xe4\xbd\xa0`).

How to we get the character at a specific index? Using [`strcharpart()`](https://neovim.io/doc/user/eval.html#strcharpart()) instead.
For example,

```vim
let my_str = '?????????'
"result will be '???', the first char in my_str
echo strcharpart(my_str, 0, 1)

" result will be '???', the second char in my_str
echo strcharpart(my_str, 1, 1)
```

We can also use the following convenience function:

```vim
function!  CharAtIdx(str,  idx)  abort
    " Get char at idx from str. Note that this is based on character indexing
    " instead of the byte index.
    return  strcharpart(a:str,  a:idx,  1)
endfunction
```

Then, to get first char of a string, use `CharAtIdx(my_str, 0)`.

# Get string length regardless of ASCII or not

This is related to the previous tip. There is function `strlen()` or `len()` in
Vim, but they only calculates byte length of a string, instead of character
length, like what `len()` in Python does. We can instead use the [`strchars()`](https://neovim.io/doc/user/eval.html#strchars()) to get string
length. I consider this one of the many hidden quirks of Vim. We just need to
get used to it.

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
