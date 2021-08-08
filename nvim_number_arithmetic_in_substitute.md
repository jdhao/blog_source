---
title: "Doing Number Arithmetics in Vim/Neovim Substitutions"
date: 2020-01-10 23:48:25+0800
tags: [Vim]
categories: [Nvim]
---

In this post, I want to share how to do number arithmetic quickly and get what
you want in specified format. More specifically, the topic is:

> How do we add, subtract, multiply or divide a value to each number in a
> range?

<!--more-->

# Example 1

Suppose that we have the following numbers at the end of each line:

```
line 1: 10
line 2: 20
line 3: 30
line 4: 40
line 5: 50
```

How to we do arithmetic operation to those numbers at the end of each line
quickly?

We can use expression in substitution command to do this task, for details, see
`:h sub-replace-expression` inside Vim. The idea is that instead of substitute
what you search with a string, we will evaluate an expression and use the
evaluated result as the replace string. The general syntax for substitution is:

```
:s/PATTERN/REPLACE/[flag]
```

To use an expression in REPLACE string, you can start the replacement string
with `\=`, and the following string will be interpreted as an expression by
Vim. If you have used capture groups in `PATTERN`, you can retrieve the
captured text in the corresponding capture group with `submatch()` function.
For example, `submatch(1)` will return the text in the 1st capture group,
`submatch(2)` returns the 2nd group, `submatch(0)` will return the whole
matching pattern.

With all this knowledge, we can easily solve the above problem. For example, to
add 2 to the number at the end of each line, do:

```vim
:1,5s/\v(\d+)$/\=str2float(submatch(1))+2
```

Note that in the above command, the return value of `submatch()` are strings.
In order to convert string to float type, we need to use function `str2float()`
explicitly. Although you can directly add strings with numbers, the result may
not be what you want. For example, `echo '0.2' + 2` shows 2 instead of 2.2.
After looking at the documentation on variables (`:h variables`), I find the
the reason:

> The Number and String types are converted automatically, depending on how they are used.

It means that strings will by default be converted to an integer where
necessary. So string `0.2` becomes 0 when we add it with 2.

Similarly, to minus or multiply by 2:

```vim
:1,5s/\v(\d+)$/\=str2float(submatch(1))-2
:1,5s/\v(\d+)$/\=str2float(submatch(1))*2
```

For division, things are a little different here, because the division
symbol `/` is the same as the substitute delimiter. In order to use division
character in substitute expression, we can [change the delimiter to other
characters](https://stackoverflow.com/q/11823616/6064933), for example, to `#`:

```vim
:1,5s#\v(\d+)$#\=str2float(submatch(1))/2.0
```

Note that for division, we need to divide those numbers by 2.0, not by 2. If
you use two integers, Vim will use integer division, not float division[^1].

One minor issue with the division result is that the result may have many
decimal digits. You may want to round it to fixed digit, e.g., 3 digits. This
can be easily accomplished with the `printf()` function, and the whole command
looks like:

```vim
:1,5s#\v(\d+)$#\=printf('%.3f', str2float(submatch(1))/2.3)
```

# Example 2

I have the following 4 columns of text (separated by more than one spaces):

```txt
data1     data2    data3       data4
12         1.0     0.1667 in   0.2150 in
16         1.0     0.2222 in   0.2825 in
20         1.0     0.2778 in   0.3488 in
24         1.0     0.3333 in   0.4163 in
28         1.0     0.3889 in   0.4838 in
32         1.0     0.4444 in   0.5513 in
```

I want to insert the result of division from 4th and 3rd column in the end of
each line. For example, after insertion, first line will become:

```
12          1.0     0.1667 in   0.2150 in       1.2897
```

My initial implementation is like:

```vim
s#\v\d{2,}\s+\d\.\d\s+(\d\.\d+) in\s+(\d\.+\d+) in#\=printf('%s  %.4f', submatch(0), submatch(2)/submatch(1))
```

However, I am getting the following result:

```
12          1.0     0.1667 in   0.2150 in  -9223372036854775808.0000
```

It seems that the number division goes wrong for string types.

As discussed earlier, `submatch()` returns a string and string will be
converted to integer implicitly when doing arithematics. So these two strings
are converted to integer instead of float type, and they all become 0, as can
be verifed by the command: `echo 0/0`.

We need to convert these strings into float first. The correct command is:

```vim
s#\v\d{2,}\s+\d\.\d\s+(\d\.\d+) in\s+(\d\.+\d+) in#\=printf('%s  %.4f', submatch(0), str2float(submatch(2))/str2float(submatch(1)))
```

We can also use `\zs` to simplify the above command a bit:

```vim
s#\v\d{2,}\s+\d\.\d\s+(\d\.\d+) in\s+(\d\.+\d+) in\zs.*#\=printf('  %.4f', str2float(submatch(2))/str2float(submatch(1)))
```

# References

+ [Number manipulation within Vim.](https://stackoverflow.com/a/18400233/6064933)
+ [Change delimiter in search and replace command.](https://stackoverflow.com/a/1684651/6064933)
+ [Increment a specific number in csv file.](https://vi.stackexchange.com/q/22469/15292)
+ [Rounding number in Vim.](https://stackoverflow.com/a/5202066/6064933)
+ [Round floats to one decimal.](https://stackoverflow.com/a/32042050/6064933)
+ [vim substitution with mathematical expression](https://stackoverflow.com/q/37805433/6064933)
+ [How can I convert px units to em units programmatically in vim?](https://stackoverflow.com/a/26387821/6064933)
+ [Search and replace: float arithmetic](https://vi.stackexchange.com/q/17934/15292)
+ https://learnvimscriptthehardway.stevelosh.com/chapters/25.html

[^1]: For example, if we do `:echo 5/2`, we will get `2` instead of `2.5`.
