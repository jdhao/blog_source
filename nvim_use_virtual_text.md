---
title: "Using Virtual Text in Neovim"
date: 2021-09-09T20:36:36+08:00
draft: false
tags: []
categories: [Nvim]
---

In Neovim, we can use the `virtual text` feature to place text anywhere in the
window, which is not possible with normal text.

<!--more-->

# A bit of history

Initially, the Nvim core dev [bfredl](https://github.com/bfredl) add the function
`nvim_buf_set_virtual_text()` to add virtual text to a buffer in [this PR](https://github.com/neovim/neovim/pull/8180).

Over the time, the code is [refactored](https://github.com/neovim/neovim/pull/15257) [and deprecated](https://github.com/neovim/neovim/pull/15184), in favor of the new API function
[`nvim_buf_set_extmark()`](https://neovim.io/doc/user/api.html#nvim_buf_set_extmark())[^2].

# Plugins utilizing virtual text

Plugins have been using virtual text to do various things. Here is a
non-exhaustive list:

+ [IndentBlankLine.nvim](https://github.com/lukas-reineke/indent-blankline.nvim): Show indent guides even on blank lines. Previously, it is not possible to place text
  on blank lines (lines with only a new line). So there is will a gap for indent
  lines when there is a blank line, see [this issue](https://github.com/Yggdroot/indentLine/issues/275).
+ [virt-column.nvim](https://github.com/lukas-reineke/virt-column.nvim): replace option `colorcolumn` using virtual text
+ [nvim-cmp](https://github.com/hrsh7th/nvim-cmp): [show function signature using virtual text](https://github.com/hrsh7th/nvim-cmp/commit/ada9ddeff71e82ad0e52c9a280a1e315a8810b9a).
+ [nvim-hlslens](https://github.com/kevinhwang91/nvim-hlslens): Show search count and index at EOL, an excellent alternative to [vim-anzu](https://github.com/osyo-manga/vim-anzu) and the likes.
+ [blamer.nvim](https://github.com/APZelos/blamer.nvim): Show line blame message at EOL[^1].
+ [Nvim-lspconfig](https://github.com/neovim/nvim-lspconfig): Show diagnostics as virtual text in the end of a line.
+ [virtual-type.nvim](https://github.com/jubnzv/virtual-types.nvim): show variable type using virtual text
+ [Extmark-toy.nvim](https://github.com/sunjon/extmark-toy.nvim): Showing cool visual effects.

# Using extmarks

The `nvim_buf_set_extmark()` function has a lot of parameters, and some of them
can be confusing for users due to the conciseness of its documentation.

Here is a working example to demonstrate its usage:

```lua
local bnr = vim.fn.bufnr('%')
local ns_id = vim.api.nvim_create_namespace('demo')

local line_num = 5
local col_num = 5

local opts = {
  end_line = 10,
  id = 1,
  virt_text = {{"demo", "IncSearch"}},
  virt_text_pos = 'overlay',
  -- virt_text_win_col = 20,
}

local mark_id = vim.api.nvim_buf_set_extmark(bnr, ns_id, line_num, col_num, opts)
```

In the above, `line_num` variable specify which line we want to put the virtual
text. `col_num` specify which column we want to put virtual text (but this is
actually only half true, since we can put virtual text at any position).
`col_num` shouldn't exceed the actual column number of the line indicated by
`line_num`. Otherwise, we get the following error:

> E5113: Error while calling lua chunk: test.lua:17: col value outside range

In the `opts` parameter, there are more options to change the behavior of this
function. `virt_text` is a table of table, where each table contains some text
and the highlight group for it.

The option `virt_text_pos` has three possible values:

+ `overlay`: overlay the virtual text on the column specified with `col_num`,
this is actually the only case the `col_num` really works.
+ `eol`: virtual text is place at the end of the line (`col_num` does not take effect).
+ `right_align`: virtual is place on the right of current window.

See the following image for a comparison:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202109150127349.png" width="800">
</p>

To actually put virtual text on any column, we should use `virt_text_win_col`
in the opts table. If we use `virt_text_win_col`, `virt_text_pos` does not take
effect any more.

To delete a virtual text, use `vim.api.nvim_buf_del_extmark(bnr, ns_id, id)`,
where `id` is the id used in `opts`.

# References

+ https://www.reddit.com/r/neovim/comments/lppnvn/indent_blankline_can_now_display_indent_guides_on/

[^1]: As of writing of this post, this plugin is still using the old API.
[^2]: In fact, extmark is more than virtual text. See `:h api-extended-marks`.
