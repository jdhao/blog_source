---
title: "How to Insert Unicode Characters in Neovim/Vim"
date: 2020-10-07T16:57:53+08:00
draft: false
tags: [Unicode, Vim]
categories: [Nvim]
---

Except for those ASCII characters, it is often not straightforward to insert
Unicode characters into Vim/Neovim. Below I will summarize different ways to
enter Unicode character inside Neovim/Vim.

<!--more-->

I assume that you are using UTF-8 encoding (`:set encoding=utf-8`), and you are
using a font with proper support for Unicode symbols
([nerd-fonts](https://github.com/ryanoasis/nerd-fonts), for example).
Otherwise, some Unicode characters shown below may not show correctly in your
terminal.

# Use Ctrl-V

The native way to insert a Unicode character is via `Ctrl-V`. In insert mode,
we first press <kbd>Ctrl</kbd> + <kbd>V</kbd>, and then for Unicode character
whose code point[^1]:

+ less than `ffff` (in hex format): press `u` (**lower** case), followed by its four-digit hex representation.
+ less than `7fffffff` (in hex format): press `U` (**upper** case), followed by its eight-digit hex representation.

For example, to insert the middle dot symbol (`·`, code point `U+00B7`), we can
press `<C-v>u00b7`. To insert the crying cat emoji (`😿`, code point `U+1F63F`),
we can press `<C-v>U0001f63f`[^2].

# Use plugin unicode.vim

To ease manipulation of Unicode characters, there is also a plugin called
[unicode.vim](https://github.com/chrisbra/unicode.vim).

Unicode.vim provides an enhanced version of the builtin `ga` (see `:h ga`)
operation. To use it, add the following setting to `init.vim`:

```vim
nmap ga <Plug>(UnicodeGA)
```

It also provides several useful commands:

+ `:UnicodeName`: show the Unicode info about character under cursor.
+ `:UnicodeSearch`: search a Unicode character. It supports regular expression.
For example, to search Unicode character containing `CAT`, we can use
`:UnicodeSearch \<CAT\>`. The bang version (`:UnicodeSearch!`) can also insert
the chosen character into cursor position.

## Insert-mode completion

Unicode.vim also provides insert mode completion for Unicode characters. We can
type some character in the name of Unicode character, and press `<C-X><C-Z>`
(i.e., <kbd>Ctrl</kbd>+<kbd>X</kbd> followed by <kbd>Ctrl</kbd>+<kbd>Z</kbd>).
Alternatively, we can also provide the Unicode code point of a character and
press `<C-X><C-Z>`.

For example, to insert middle dot, we can:

+ type `dot`, and press `<C-X><C-Z>` (A completion menu will pop up with candidate character).
    <p align="center">
    <img src="https://blog-resource-1257868508.file.myqcloud.com/20201007223300.jpg" width="600">
    </p>

+ type `U+00b7`, and press `<C-X><C-Z>`.

If you have [fzf](https://github.com/junegunn/fzf) installed, you can also
activate the fuzzy search feature to search Unicode (see `:h
unicode-fuzzy-insertion`).

# Use digraphs

Vim/Neovim also provides a feature called digraphs (see `:h digraphs`). Some
non-printed character or commonly-used Unicode characters can be easily
inserted using this feature. The command `:digraphs` will print the pre-defined
digraphs table, like the following:

```
......
PI ¶  182    pp ¶  182    .M ·  183    ~. ·  183    ', ¸  184    1S ¹  185
A' Á  193    A> Â  194    A^ Â  194    A? Ã  195    A~ Ã  195    A: Ä  196
......
```

In each digraph group, the first two characters are the codes for the actual
character, the middle being the actual character, and the last being the
decimal value of the characters. For example, the codes for middle dot is `.M`,
and its decimal number is 183.

To enter a character, press `Ctrl-K` in insert mode, followed by its codes. For
example, to insert middle dot using digraphs, press `<C-K>.M`. To insert the
copyright symbol (`©`), press `<C-K>Co`. To insert trademark symbol (`™`),
press `<C-K>TM`.

## Define your own digraphs

We can also define our own digraphs. The syntax is:

```vim
:digraphs {char1}{char2} {number}
```

`{number}` is the decimal value for the Unicode character. For example, to define a digraph entry
for the loud crying face emoji (`😭`, decimal value 128557), use the following command:

```vim
:digraphs lc 128557
```

Then, in insert mode, pressing `<C-K>lc` will insert this emoji.

# References

+ [How to enter non-ascii characters using hex or octal codes in vi?](https://unix.stackexchange.com/q/61118/221410)
+ [What is the easiest way to insert Unicode characters into a document?](https://vi.stackexchange.com/q/2254/15292)
+ [Enter Unicode characters with 8-digit hex code](https://stackoverflow.com/q/9119649/6064933)
+ https://sanctum.geek.nz/arabesque/special-characters-in-vim/

[^1]: See `:h i_CTRL-V_digit` inside Neovim/Vim for the official doc.
[^2]: Since the crying cat emoji has 5-digit hex, we have to use upper case `U` followed by 8-digit hex number. The three leading zeros can not be omitted
