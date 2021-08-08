---
title: "Creating Markdown Front Matter with Ultisnips"
date: 2019-12-22 13:45:25+0800
tags: [Vim, Markdown, snippet]
categories: [Nvim]
---

# Introduction #

I write my blog posts in Markdown and build the blog using
[Hugo](https://gohugo.io/). Hugo supports [front matter](https://gohugo.io/content-management/front-matter/)
for Markdown so that you can attach some metadata with a post, such as title,
date, tags, categories, etc.

<!--more-->

The front matter for a post is something like the following:

```yaml
---
title: "Creating Markdown Front Matter with Ultisnips"
date: 2019-12-22 13:45:25+0800
tags: [Vim, Markdown]
categories: [Nvim]
---
```

The `date` field is in ISO date format.

# Initial snippet #

It is tedious to write the boilerplate text in front matter for every post I
create. [Ultisnips](https://github.com/SirVer/ultisnips) is good at automating
such tasks. If you are not familiar with Ultisnips, you can check [my previous
post](https://jdhao.github.io/2019/04/17/neovim_snippet_s1/) on setting up
Ultisnips.

So the initial snippet I wrote looks like this:

```yaml
---
snippet meta "Markdown front matter in YAML format" b
title: "${1:TITLE TEXT}"
date: $2
tags: [$3]
categories: [$4]
---
```

When we type the trigger word `meta` at the beginning of a line[^2] and press
<kbd>Tab</kbd>, it will be expanded. We can jump forward and backward among the
tabstops to fill the text.

This snippet is working great, but for the `date` field, we still need to fill
the current date manually, which is not convenient enough.

# Ultisnips command interpolation #

Ultisnips also provides a powerful feature called interpolation, which means
that you can run shell, Vim and Python command inside a snippet and use the
returned output in snippet. The interpolation code are placed inside two
backticks (`` `CODE` ``) and opens up many possibilities. For shell command
interpolation, you just write the shell command inside the backticks. For Vim
and Python code interpolation, you should follow the opening backtick with `!v`
and `!p` respectively.

For example, to get the current date in ISO format, i.e., `2019-12-22
14:43:43+0800`, using the above three interpolation, the code is like:

+ shell interpolation: `` `date "+%Y-%m-%d %H:%M:%S%z"` ``
+ Vim interpolation: `` `!v strftime("%Y-%m-%d %H:%M:%S%z")` ``
+ Python interpolation:
    ```
    `!p from datetime import datetime
    snip.rv=datetime.now().strftime("%Y-%m-%d %H:%M:%S%z")`
    ```

Back to the above snippet, if we want the interpolation code to be
cross-platform, we should only consider Vim or Python interpolation since most
Linux shell commands are not available on Windows.

## Vim interpolation ##

With Vim interpolation, the original meta snippet for Markdown becomes:

```yaml
snippet "meta(data)?" "Markdown metadata front matter" br
---
title: "$1"
date: `!v strftime("%Y-%m-%d %H:%M:%S%z")`
tags: [$2]
categories: [$3]
---
```

One minor issue with this snippet is that the date value keeps changing while
we are inside the snippet region[^1], which is not desired. According to
[tutorial here](https://www.youtube.com/watch?v=JJQYwt6Diro), we can use Python
interpolation to deal with this issue.

## Python interpolation ##

Inside the Python interpolation code, Ultisnips provides the `snip` object. The
attribute `snip.c` is empty once the interpolation has finished. `snip.rv` is
the result for the snip interpolation.

We can check if `snip.c` is empty to fix the date once the interpolation code
is run for once.

The new `meta` snippet for Markdown now becomes:

```yaml
snippet "meta" "Markdown metadata front matter" b
---
title: "$1"
date: `!p from datetime import datetime
if not snip.c:
    snip.rv=datetime.now().strftime("%Y-%m-%d %H:%M:%S%z")`
tags: [$2]
categories: [$3]
---
```

Now the date field will be fixed while we are editing other parts of the
snippet.

[^1]: Because inside the snippet region, the interpolation code is run in real time and the result is updated to reflect the change.

[^2]: The `b` flag says that this snippet can only be triggered at the begining of a line.

# References #

+ [Ultisnips official tutorial.](https://www.youtube.com/watch?v=JJQYwt6Diro)
+ [Vimcast on Ultisnips Python interpolation.](http://vimcasts.org/episodes/ultisnips-python-interpolation/)
