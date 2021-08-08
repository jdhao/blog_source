---
title: "A Better Mapping to Leave Insert Mode than jk"
date: 2020-11-23T22:26:19+08:00
draft: false
tags: []
categories: [Nvim]
---

<font color="blue">Update 2021-01-14: I ended up writing a plugin called
[better-escape.vim](https://github.com/jdhao/better-escape.vim) which aims at
solving this issue.</font>

A [very popular insert mode mapping](https://vi.stackexchange.com/q/300/15292)
for Neovim/Vim is to map `jk` or `kj` or `jj` to `<ESC>` for quicker escaping
from the insert mode. I have used the following mapping for quite some time:

```vim
inoremap jk <ESC>
```

<!--more-->

However, it will cause lag whenever we press `j` in insert mode. Because Vim
will wait for [`timeoutlen`](https://neovim.io/doc/user/options.html#'timeoutlen')
milliseconds to see if you want to enter `j` or if you mean the map `jk`.

Of course, you can reduce `timeoutlen` option to very small values, but it is
not user-friendly to type mappings that consists of several key strokes. Before
you can press the next key in your mapping, Vim may have already time out.

I saw on Reddit the other day that someone propose to check the character
before the current one and leave insert mode based on some conditions. I
thought this might be a better way. The idea is to have an insert mode mapping
for `k`, when we press `k`, we then check the character before k. If that
character is `j`, we will erase j and leave insert mode. Otherwise, we will
insert k as is.

Here is a crude implementation of that idea:

```vim
scriptencoding utf-8

inoremap <expr> k EscapeInsertOrNot()

" some test text
function! EscapeInsertOrNot() abort
  " If k is preceded by j, then remove j and go to normal mode.
  let line_text = getline('.')
  let cur_ch_idx = CursorCharIdx()
  let pre_char = CharAtIdx(line_text, cur_ch_idx-1)
  echom 'pre_char is:' pre_char
  if pre_char ==# 'j'
    return "\b\e"
  else
    return 'k'
  endif
endfunction

" split(line_text, '\zs') can split string into separate char
"汉字测试这是一些汉字 some charjust
" byte index of 这 is 14 (using col('.'))

" let my_str = '你好吗'
" strcharpart(my_str, 0, 1) is the first char in my_str (it is like my_str[0] in Python)
" strcharpart(my_str, 1, 1) is the second char in my_str

" let ch = '你'
" byteidx(ch, 1) is the number of byte in UTF-8 encoding for ch (suppose
" that the character encoding is UTF-8)

function! CharAtIdx(str, idx) abort
  " Get char at idx from str. Note that this is based on character index
  " instead of the byte index.
  return strcharpart(a:str, a:idx, 1)
endfunction

function! CursorCharIdx() abort
  " This function returns the character-based index for character under
  " cursor.

  " Get the character under cursor
  let line_text = getline('.')
  let cur_byte_idx = col('.')
  echo 'cur_byte_idx:' cur_byte_idx

  if cur_byte_idx == 1
    echomsg 'cursor char idx:' 0
    return 0
  endif

  " character index starts from zero
  let [ch_idx, byte_idx] = [-1, 0]

  for c in split(line_text, '\zs')
    let ch_idx += 1
    let byte_idx += byteidx(c, 1)
    echomsg ch_idx c byte_idx

    if byte_idx+1 == cur_byte_idx
      let pre_char = strcharpart(line_text, ch_idx, 1)
      echomsg 'pre char is:' pre_char 'pre char index:' ch_idx

      let cursor_char = strcharpart(line_text, ch_idx+1, 1)
      echomsg 'cursor char' cursor_char 'index:' ch_idx+1

      return ch_idx + 1
    endif
  endfor
endfunction
```

Note that the above script is a little complex, because we need to take
non-ASCII characters into account. The function `CursorCharIdx()` is used to
get the character index of the cursor char in the cursor line. I have tested
that it works for pure ASCII text and text containing non-ASCII characters.

I feel that the function above to get the cursor char index is too complex. So
I asked [a question](https://vi.stackexchange.com/q/28126/15292) on
stackexchange and got a more concise solution:

```vim
function! CursorCharIdx() abort
  " A more concise way to get character index under cursor.
  let cursor_byte_idx = col('.')
  if cursor_byte_idx == 1
    return 0
  endif

  let pre_cursor_text = getline('.')[:col('.')-2]
  return strchars(pre_cursor_text)
endfunction
```

One issue is that if you want to insert `jk` literally, you can not just type
`j` followed by `k`. It will be interpreted as escaping the insert mode. To
insert k, we can press `Ctrl-V`, then press `k`. This works, because Vim will
not consider character after `Ctrl-v` for mappings. See also `:h i_CTRL-V` for
the details. Since I rarely use `jk` in my writing, I am fine with this issue.

# References

+ http://derekwyatt.org/2015/07/27/getting-character-under-cursor-in-vim/
+ [Vim: do something in a function based on character under a cursor?](https://stackoverflow.com/q/7560911/6064933)
+ [Vim (vimscript) get exact character under the cursor](https://stackoverflow.com/q/23323747/6064933)
+ [How can I get the character at the cursor position in a multibyte aware manner?](https://vi.stackexchange.com/q/11476/15292)
+ [Reliably get the character at a byte index in a string](https://vi.stackexchange.com/q/19694/15292)
