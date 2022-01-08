---
title: "A Guide on Creating Your Own Text Objects in Neovim/Vim from Scratch"
date: 2020-11-15T15:00:06+08:00
draft: false
tags: [text-object]
categories: [Nvim]
---

Shortly after I started using [Neovim](https://github.com/neovim/neovim), I
learned about the concept of [text objects](https://neovim.io/doc/user/motion.html#text-objects).
For example, when the cursor is inside a pair of `[]`, we can use `ci]` to
change text inside them. This is definitely one of the greatest moments on my
road of learning Vim. However, I haven't really thought about how does text
object really work.

<!--more-->

Since I use Markdown frequently, I often need to change URLs quickly. I thought
it might be a good idea to define a text object for URL. Actually, there are already
[some](https://github.com/mattn/vim-textobj-url) [plugins](https://github.com/LeonB/vim-textobj-url) for it. However, they all rely on another
plugin. Besides, I also want to learn how to write my own text objects from
scratch. So I read some documentation and online posts. Now, I am able to
create my own text objects with ease.

# How to define new text objects

We can think of a text object as a piece of text. **It is visually selected
when we press the shortcut that defines it**. When we want to define a new text
objects, all we need to figure out is: how do we select it when we press the
shortcut?

To select the desired text, it boils down to understanding two things:

+ The start and end position of this text object
+ Is it character-wise or line-wise or even block-wise (I haven't found block-wise useful, though)?

Now, it is easy to define new text objects. All we do is to make sure that the
desired text is selected when we press the shortcut. It does not really matter
how do you achieve that with Vim script.

For example, if we want to define a text object for **inner** line (excluding
blank space at head and tail of current line). Here is one such implementation:

```vim
xnoremap il ^og_
onoremap il :normal vil<CR>
```

In the above xnoremap, `o` is used to move cursor from on end of visual
selection to another end, see [`:h v_o`](https://neovim.io/doc/user/visual.html#v_o).

# Text object for URL

To define a text object for URL, we need to first decide the complete URL text
under cursor, find its start and end position in current line, then select the
entire URL text in visual mode (char-wise not line-wise).

```vim
vnoremap <silent> iu :<C-U>call <SID>URLTextObj()<CR>
onoremap <silent> iu :<C-U>call <SID>URLTextObj()<CR>

function! s:URLTextObj() abort
  if match(&runtimepath, 'vim-highlighturl') != -1
    " Note that we use https://github.com/itchyny/vim-highlighturl to get the URL pattern.
    let url_pattern = highlighturl#default_pattern()
  else
    let url_pattern = expand('<cfile>')
    " Since expand('<cfile>') also works for normal words, we need to check if
    " this is really URL using heuristics, e.g., URL length.
    if len(url_pattern) <= 10
      return
    endif
  endif

  " We need to find all possible URL on this line and their start, end idx.
  " Then find where current cursor is, and decide if cursor is on one of the
  " URLs.
  let line_text = getline('.')
  let url_infos = []

  let [_url, _idx_start, _idx_end] = matchstrpos(line_text, url_pattern)
  while _url !=# ''
    let url_infos += [[_url, _idx_start+1, _idx_end]]
    let [_url, _idx_start, _idx_end] = matchstrpos(line_text, url_pattern, _idx_end)
  endwhile

  " echo url_infos
  " If no URL is found, do nothing.
  if len(url_infos) == 0
    return
  endif

  let [start_col, end_col] = [-1, -1]
  " If URL is found, find if cursor is on it.
  let [buf_num, cur_row, cur_col] = getcurpos()[0:2]
  for url_info in url_infos
    " echo url_info
    let [_url, _idx_start, _idx_end] = url_info
    if cur_col >= _idx_start && cur_col <= _idx_end
      let start_col = _idx_start
      let end_col = _idx_end
      break
    endif
  endfor

  " Cursor is not on a URL, do nothing.
  if start_col == -1
    return
  endif

  " " now set the '< and '> mark
  call setpos("'<", [buf_num, cur_row, start_col, 0])
  call setpos("'>", [buf_num, cur_row, end_col, 0])
  normal! gv
endfunction
```

In the above code, we first find the end and start index of all URLs in current
line. We then check if current cursor position is on one of those URLs. If that
is true, we then set the `'<` and `'>` mark to the start and end index of this
URL. Finally, we use `gv` to select this URL (see `:h gv`) .

In the above code, we use the URL pattern from a plugin called
[vim-highlighturl](https://github.com/itchyny/vim-highlighturl) if it is
installed or use [`expand("<cfile>")`](https://jdhao.github.io/2020/11/11/nifty_nvim_techniques_s8/#copy-url-under-cursor-into-a-register)
to get the URL under cursor. Note that `expand("<cfile>")` also works for
non-URL text, the text object `iu` will also work for non-URL text. It is
better to use the URL pattern provided by vim-highlighturl.

There is another shorter, but probably *hacky* way to define the URL text
object:

```vim
function! s:URLTextObj() abort
  " We need to find the start and end of URL, and select the entire URL string.
  " the following does not work well, since it also shows results even when
  " cursor is not on a valid URL.
  " let url = expand('<cfile>')
  " a better way to get url.
  let url = matchstr(getline('.'), highlighturl#default_pattern())
  normal! ^
  " We should escape special patterns in url, see https://superuser.com/q/320398/736190,
  " see also https://stackoverflow.com/a/46235399/6064933
  let url = escape(url, '/')
  " I got the inspiration here: https://vi.stackexchange.com/a/2925/15292
  silent! execute "normal! /\\V" . url . "/s\<CR>v//e\<CR>"
endfunction
```

Note that you are **not** required to define URL text object as `iu`, any valid
LHS for mappings is acceptable. We use `iu` because it conforms to the naming
style of native text objects.

# A text object for Markdown code blocks

For Markdown code blocks, it is necessary to define both inner and around text
objects. We will use `ic` and `ac` to denote **inner** code block and
**around** code block, respectively.

Here is a sample implementation[^2]:

```vim
vnoremap <silent> ic :<C-U>call <SID>MdCodeBlockTextObj('i')<CR>
onoremap <silent> ic :<C-U>call <SID>MdCodeBlockTextObj('i')<CR>

vnoremap <silent> ac :<C-U>call <SID>MdCodeBlockTextObj('a')<CR>
onoremap <silent> ac :<C-U>call <SID>MdCodeBlockTextObj('a')<CR>

function! s:MdCodeBlockTextObj(type) abort
  " the parameter type specify whether it is inner text objects or around
  " text objects.

  " Move the cursor to the end of line in case that cursor is on the opening
  " of a code block. Actually, there are still issues if the cursor is on the
  " closing of a code block. In this case, the start row of code blocks would
  " be wrong. Unless we can match code blocks, it is not easy to fix this.
  normal! $
  let start_row = searchpos('\s*```', 'bnW')[0]
  let end_row = searchpos('\s*```', 'nW')[0]

  let buf_num = bufnr()
  " For inner code blocks, remove the start and end line containing backticks.
  if a:type ==# 'i'
    let start_row += 1
    let end_row -= 1
  endif
  " echo a:type start_row end_row

  call setpos("'<", [buf_num, start_row, 1, 0])
  call setpos("'>", [buf_num, end_row, 1, 0])
  execute 'normal! `<V`>'
endfunction
```

Since code blocks are line-wise inherently, we only need to decide its start
and end row. With this info, we can set the `'<` and `'>` mark. Then we can
easily select the desire text. This time, we use visual-line mode, since it
makes more sense to select code blocks in visual-line mode.

# Conclusion

In this post, I have introduced how does text object work and how to define
your own text objects. The most important part is to know your text object well
and find ways to visually select it when the corresponding shortcut is pressed.

# References

+ [vim wiki: create new text objects](https://vim.fandom.com/wiki/Creating_new_text_objects)
+ [Is there a text object for current line?](https://vi.stackexchange.com/q/6101/15292)
+ [Is there a text object for the entire buffer?](https://vi.stackexchange.com/q/2319/15292)
+ A plugin to create user text objects easily: https://github.com/kana/vim-textobj-user
+ https://25.wf/posts/2020-09-04-vim-markdown-text-object.html
+ https://vimways.org/2018/transactions-pending/

[^1]: Operator pending mode is activated when we use some operations that should be followed by motions or text objects, e.g., when we press `d`, `c`, `gq`. See also `:h operator`.
[^2]: Other implementations can be found in [this issue](https://github.com/plasticboy/vim-markdown/issues/282).
