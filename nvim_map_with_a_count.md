---
title: "Create Mappings That Take A Count in Neovim"
date: 2019-04-29 23:08:16+0800
tags: []
categories: [Nvim]
---

![Mappings](https://blog-resource-1257868508.file.myqcloud.com/20190503204410.png)

Many normal mode commands accept a `count`, which means to repeat the motion
`count` times. For example, `3j` moves the cursor 3 lines below the current
line and `4w` will move the cursor four words forward. Usually, the
user-defined mappings can not take a count. Even if they can, they will most
probably not work the way you expect them to. In this post, I will describe
what I have learned to make a fairly complex mapping repeatable with a count.

<!--more-->

# The Problem

I find myself often doing something like adding one or two blank lines below
the current line to separate the structure of the code or text. So I created a
mapping for this operation after some search:

```
nnoremap oo :execute "normal! m`o\<Esc>``"<cr>
```

Unfortunately, the above mapping does not work[^3], it will print an error:

> E114: Missing quote: "normal! m\`a\\

The above mapping does not work because the way we escape <kbd>Esc</kbd> is not
correct. When you press `oo`, it is like we have typed `` :execute "normal! m`o
`` in the command line and then press the <kbd>Esc</kbd> key.

You have to use either one of the following mappings to indicate
that you want to press the `<Esc>` key, not type `<`, `E`, `s`, `c`, `>`
literally:

```
nnoremap oo :execute "normal! m`o\<lt>Esc>``"<cr>
nnoremap oo :execute "normal! m`o<c-v><Esc>``"<cr>
nnoremap oo :execute "normal! m`o\e``"<cr>
```

This is becoming too complicated. `nnoremap` is actually equivalent to `normal!`.
So we can simply the above mappings to avoid complications:

```
nnoremap oo m`o<Esc>``
```

In the above mapping, we first create a mark `` ` `` for the initial cursor
position, the insert a newline, go back to normal mode and restore the cursor
position[^1]. Then end result is that we insert a newline and maintain the
cursor position.

But this mapping only works once, i.e., it can not take a count. In order to
add two lines below, we have to press `oo` two times, which is not ideal for
me.

After searching the Internet, I found this [vim cast](http://vimcasts.org/episodes/creating-mappings-that-accept-a-count/),
which address this issue specifically. It introduced two ways to create a
mapping that accepts a count.

# Two Solutions

## The imperfect one -- using `:normal` command

The first way is to use `:normal` command on the right-hand side of the
mapping. The `:normal {expr}` command will execute `{expr}` as normal mode
command just as you have typed them in normal mode. For example, if you execute
`:normal G` in Vim command line, the cursor will be put at the last line of
current buffer.

The `:normal` command will take into account the mappings you have defined. If
you have defined `G` to other command, you may not get what you want. To remedy
this, Vim also provides a bang version: `:normal!`, which will use Vim's
default mapping for the key.

The `:normal!` command can take an optional `{range}`, which means to execute the
`{expr}` for current cursor line as well as lines indicated by `{range}`[^2].

Combining the above knowledge, we can create the following mapping:

```
nnoremap oo :normal! m`o<Esc>``
```

This mapping can take a count. If you press `2oo` in normal mode, two lines
will be insert below current line. But you will notice that the position of the
cursor is changed because the `:normal!` command with range will move the
cursors:

> Before executing the {commands}, the cursor is positioned in the first column
> of the range, for each line.

There is also a pitfall when using this mapping in the last line of current
buffer. If you use a count bigger than one, you will get an error:

> E16: Invalid range

since there is no lines below the current line.

## The better -- using the expression register `@=`

Another method is to use [expression
register](http://vimcasts.org/episodes/simple-calculations-with-vims-expression-register/)[^4].
The expression register can store a command string or store the result of some
functions. The mapping we will use is:

```
nnoremap oo @='m`o<C-V><Esc>``'<CR>
```

Here, we define the command we will use in the expression register. Note that
to represent <kbd>Esc</kbd>, we have to precede it with `<C-v>`. This mapping
can take a count and work as expected (the cursor is moved).

Also note that the below mappings do not work:

```
nnoremap oo @='m`o\<lt>Esc>``'<cr>
nnoremap oo @='m`o\e``'<cr>
```

I do not know why. But they just do not work any more. If anyone knows the
reason, do not hesitate to tell me, please.

**Update: 2019-05-12**

Note if we change the single quote to double quote, the following mappings
using the expression register will work as expected.

```
nnoremap oo @="m`o\<lt>Esc>``"<cr>
nnoremap oo @="m`o\e``"<cr>
```

It seems like an issue with single quote, where every character inside is
interpreted literally.

# The best

Although using the expression register mapping solves the problem, it is error
prone when writing such expressions without considerable familiarity with the
various oddities of Vim.

I posted [my question on Reddit](https://www.reddit.com/r/vim/comments/bj0fip/any_doc_on_how_to_escape_special_keys_inside/)
and received a few replies. A new solution based on `<expr>` (see `:h
map-<expr>`) emerged and it seems the best solution ever:

```
nnoremap <expr> oo 'm`' . v:count1 . 'o<Esc>``'
nnoremap <expr> OO 'm`' . v:count1 . 'O<Esc>``'
```

The expression mapping will evaluate the RHS string before executing it. `v:
count1` is a special Vim variable. It is the count you supplied to this
mapping. If no count is given, it has the default value of 1 (see `:h v:count1`).

This mapping has two benefits. One is that it is clearer. The second is that it
executes faster than expression register mappings where the mapping is repeated
`count` times and you can see obvious lags between each mapping.

# A special note about backslash in command line and Vim script

During the process, I have also learned that the meaning of ` \ ` may change
depending on the `B` flag in `cpoptions`.

Suppose that we have defined the following insert mode mapping in command line
or in Vim script.

```
imap <c-a> \<Home>
```

When you press `<C-a>` in insert mode, the output will depend on if `B` flag is
in `cpoptions`.

## with `B` flag in `cpoptions`

Backslash is take literally as is. A backslash is inserted and the cursor is
moved to the beginning of cursor line.

## without `B` flag in `cpotions`

Backlash will escape `<` the character. When you press `<c-a>` in insert mode,
the 6 characters (i.e., `<`, `H`, `o`, `m`, `e`, `>`) is inserted.

When define mappings, the `<` should be handled carefully, or you will not get
what you want.

# Conclusion

In this post, I have summarized how to define a map that can accept a count.
Overall, defining the map via expression register is preferred and works great.
Extra care must be paid when you include special keys such as <kbd>Esc</kbd>
inside the expression register.

# References

+ [Create a mapping to insert a new line below](https://stackoverflow.com/q/16134457/6064933).
+ [`:normal` command explained and why should we use `:normal!`](http://learnvimscriptthehardway.stevelosh.com/chapters/29.html)
+ [combine `:execute` and `:normal` command (also about how to represent <Esc>)](http://learnvimscriptthehardway.stevelosh.com/chapters/30.html).
+ For using `<Esc>` inside `:execute` string in script mappings, see [here](https://vi.stackexchange.com/q/3721/15292) and [here](https://stackoverflow.com/q/41538389/6064933).
+ [Supply a count to a map](https://vim.fandom.com/wiki/Mapping_keys_in_Vim_-_Tutorial_(Part_2)).

[^1]: `` `M `` will move the cursor to the position marked by marker `M`, see `:h mark-motions`.
[^2]: See `:h :normal-range` for more information.
[^3]: Note that in command line, ``` :execute "normal! m`o\<Esc>``" ``` will work, also see `:h expr-quote`. But it does not work in mappings inside a Vim script. It is f**king complicated!
[^4]: see also `:h quote_=`
