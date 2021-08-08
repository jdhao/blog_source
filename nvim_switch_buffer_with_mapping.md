---
title: "Switching Buffers in Neovim with mapping"
date: 2020-10-16T23:31:37+08:00
draft: false
tags: []
categories: [Nvim]
---

I usually use `{num}Ctrl-6` to switch buffers in Neovim, where `{num}` is the
number of buffer (see [this post](https://jdhao.github.io/2018/09/29/Switching_buffers_quickly_Neovim/)).
Since Neovim has builtin `gt` mapping to switch between tabpages, I thought it
might be a good idea to use `gb` as buffer switching shortcut avoid strech when
pressing `Ctrl-6`.

<!--more-->

Here is what I have got:

<details>
<summary><font size="2" color="red">Click to show the code.</font></summary>

```vim
nnoremap <silent> gb :<C-U>call <SID>GoToBuffer(v:count, 'forward')<CR>
nnoremap <silent> gB :<C-U>call <SID>GoToBuffer(v:count, 'backward')<CR>

function! s:GoToBuffer(count, direction) abort
  if a:count == 0
    if a:direction ==# 'forward'
      bnext
    elseif a:direction ==# 'backward'
      bprevious
    else
      echoerr 'Bad argument ' a:direction
    endif
    return
  endif
  " Check the validity of buffer number.
  if index(s:GetBufNums(), a:count) == -1
    echohl WarningMsg | echomsg 'Invalid bufnr: ' a:count | echohl None
    return
  endif

  if a:direction ==# 'forward'
    silent execute('buffer' . a:count)
  endif
endfunction

function! s:GetBufNums() abort
  let l:buf_infos = getbufinfo({'buflisted':1})
  let l:buf_nums = map(l:buf_infos, "v:val['bufnr']")
  return l:buf_nums
endfunction
```
</details>

You can use `gb` and `gB` without count to switch forward and backward in the
buffer list. When provided with a count, `gb` can also switch the buffer with
that number. For example, if we have buffer with number 6, press `6gb` in
normal mode will switch to that buffer.

# Issues I have met

## Using count in mappings

If we use a count before the mapping, and then execute some command, vim will
insert a range for this command automatically[^1]. To prevent this, we use `Ctrl-U`
to delete the range inserted by Vim.


Refs:

+ [Why do some Vim mappings include <C-U> after a colon?](https://stackoverflow.com/q/13830874/6064933)
+ [Creating a Vim function mapping that takes a count](https://vi.stackexchange.com/a/4384/15292)

## s:MyFunc or \<SID\>MyFunc

Another issue is that we need to use `call <SID>GoToBuffer` instead of `call
s:GoToBuffer`. The `s:` notation only works if we are in the same script that
defines this function. When we execute the mapping, we are usually in other
files. If we use `s:` notation, we get the following error:

    > E81: Using <SID> not in a script context

See also Neovim help file on this topic (`h <SID>`).

Ref:

+ http://vim.1045645.n5.nabble.com/lt-SID-gt-or-s-General-questions-about-functions-td4297515.html

[^1]: To verify, press a number, and then press `:` to enter command line mode.
