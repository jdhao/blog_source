---
title: "Regex Keyword and Python Interpolation in Ultisnips"
date: 2020-01-05 18:52:03+0800
draft: false
tags: [snippet]
categories: [Nvim]
---

[Vim-snippet]() provides a lot of useful Ultisnips snippets for various
filetypes. For example, for Markdown, to insert level 1 header, the snippet
trigger is `sec`, and for level 2 and 3 header, the triggers are `ssec` and
`sssec` respectively. These triggers are non-intuitive and hard to remember and
type.

<!--more-->

Today, I want to share how to create just one Markdown header snippet using
regex keyword and Python interpolation[^1] with
[Ultisnips](https://github.com/SirVer/ultisnips). The final effect of the
snippet is that you can type `h{NUM}` (where `NUM` is a header level) and press
<kbd>Tab</kbd> will create the corresponding headers.

# Creating a header snippet

## The regex keyword ##

In order to create a single snippet with chaning trigger keyword, we need to
use regex in the trigger word. So the first line of the snippet looks like
this:

```
snippet "h([1-6])" "Markdown header" br
```

Note the `br` flag at the end of the line:

+ `b`: This snippet only triggers at the beginning of a line.
+ `r`: This snippet contains regex and is not a normal snippet.

The idea is that we will capture the number after `h` in the begining of a
line. This number must be in the range [1-6] to trigger the snippet since there
are only six header levels in Markdown.

Also note that, unlike other snippet trigger, the regex trigger word needed to
be quoted with double quotes.

## A simple snippet ##

Combining regex keyword and Python interpolation, we can create a simple header
snippet like the following one:

```snippet
snippet "h([1-6])" "Markdown header" br
`!p snip.rv = int(match.group(1))*'#'` ${1: Section Title} `!p snip.rv = int(match.group(1))*'#'`

$0
endsnippet
```

In the above snippet, we use two same Python interopolation blocks to create
the left and right header level marker, and a regular snippet text is between
them.

Inside Python interpolation block, Ultisnips provides the `match` object, which
is the captured result from regex keyword. `match.group(1)` will be the content
of the first capture group, and in this case, it will be the number in string
format. The rest of the code is self-explaining so I will not elaborate.


## A complex one using post_jump ##

A more **advanced** way is to use `post_jump` actions, which can perform
various complicated logics inside a Python function.

With post_jump, the header snippet now looks like this:

```snippet
global !p
def gen_header(snip):
    placeholders_string = snip.buffer[snip.line].strip()
    level = int(placeholders_string[0])

    # erase current line
    snip.buffer[snip.line] = ""
    line_content = "#"*level + " ${1:Section Name} " + "#"*level
    line_content += '\n\n$0'

    snip.expand_anon(line_content)
endglobal


post_jump "gen_header(snip)"
snippet "h([1-6])" "Markdown header" br
`!p snip.rv = match.group(1)`
endsnippet
```

In the post jump code, `snip.buffer` is the object representing the current
buffer and is indexable. `snip.line` is the line in which the snippet expand.

`snip.expand_anon()` will accept the snippet content and expand it, just like
you have typed it in the snippet body.

# References

+ https://github.com/honza/vim-snippets/blob/master/UltiSnips/markdown.snippets
+ http://vimcasts.org/episodes/ultisnips-python-interpolation/

[^1]: If you are not familiar with Python interpolation, check tutorial
  [here](https://www.youtube.com/watch?v=JJQYwt6Diro) and post [here](https://jdhao.github.io/2019/12/22/ultisnips_avoid_content_update/).
