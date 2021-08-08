---
title: "An Introduction to Lookaround Regular Expression in Neovim/Vim"
date: 2018-10-18 19:34:00 +0800
tags: [Vim, Regex]
categories: [Nvim]
---

Today I want to use some regular expressions for searching and replacing in my
files. But I found that Nvim/Vim regular expression engine has its own flavor,
which is different from the regex engine used by Sublime Text[^2]. I ended up
learning some of the basics of Nvim-style regex. In this post, I want to share
about how to use lookaround[^1] in Nvim.

<!--more-->

# Lookaround in Nvim

First, in Nvim, the syntax for look around is:

+ `\@=`: positive lookahead
+ `\@!`: negative lookahead
+ `\@<=`: positive lookbehind
+ `\@<!`: negative lookbehind

But how to use look around in Nvim? Let is take an example to explain this.
Suppose we have text as follows:

```
foobar foo barfoo
```

To match `foo` which is followed by `bar` (`bar` not part of the match), use
the following regex:

```
/foo\(bar\)\@=
```

In Nvim, `(` and `)` matches literally, which is different from
[PCRE](https://en.wikipedia.org/wiki/Perl_Compatible_Regular_Expressions). In
order to use lookaround in Nvim, we have to include the lookaround pattern in a
group, hence the escaped parentheses `\(\)` around `bar`.

So the first `foo` in the above text matches:

```
foobar foo barfoo
^
one match here
```

Similarly, we have the following 4 patterns:

1. Match `foo` which is not followed by `bar`: `/foo\(bar\)\@!`

```
# now we have two matches
foobar foo barfoo
       ^      ^
       two matches here
```

2. Match `foo` which is preceded by `bar`: `/\(bar\)\@<=foo`

```
foobar foo barfoo
              ^
              one match here
```

3. Match `foo` which is not preceded by `bar`: `/\(bar\)\@<!foo`

```
foobar foo barfoo
^      ^
two matches here
```

4. Match `foo` which is neither followed nor preceded by `bar`:
   `/\(bar\)\@<!foo\(bar\)\@!`

```
foobar foo barfoo
       ^
       one match here
```

## Combine magic mode and look around

Several months after I first wrote this article, I learned that Nvim also has a
`very magic` mode (see `:h /magic` for more info) which behaves more like
[PCRE](https://en.wikipedia.org/wiki/Perl_Compatible_Regular_Expressions).  The
`very magic` mode is activate by preceding the search pattern with `\v`. In the
`very magic` mode, you have to remove the ` \ ` character from the look around
pattern. So in the very magic mode, the following regex should be used instead:

+ positive look ahead: `@=`
+ negative look ahead: `@!`
+ positive look behind: `@<=`
+ negative look behind: `@<!`

For example, to match `foo` which is followed by `bar`, you should use:

```vim
\vfoo(bar)@=
```

# `\ze` and `\zs` for positive lookaround

We may also use `\zs` and `ze` for positive lookbehind and lookahead. `\zs`
means that the actual match starts from here and `\ze` means that the actual
match ends here. Take the above `foobar` text as an example:

+ Match `foo` preceded by `bar`: `\bar\zsfoo`
+ Match `foo` followed by `bar`: `foo\zebar`

In the above two match, `bar` is not part of the match. You can see that`\ze`
and `\zs` simplify the pattern for positive lookaround.

# A last example

Let's take another example (adapted from [this
post](https://stackoverflow.com/questions/8169937/positive-look-behind-assertion-and-substitution-with-vim/52871369#52871369)):

```
rs11223-A        -A
rs23300-G        -TTA
rs9733-T          -G
rs11900000-GT    -TTG
```

Suppose we want to find `-` in the first part of each line, how do we write the
regex pattern?

## Using positive lookbehind

With positive lookbehind, the pattern can be (definitely not the only one):

```
/\(\d\+\)\@<=-
```

In the above regex pattern, `\+` means to match previous pattern 1 or more
time, which is a bit different from what we are accustomed to in Sublime Text.

## Using `\zs`

With `\zs`, we can simplify the match pattern:

```
/\d\+\zs-
```

Ok, that is all. Hope you can finally understand how to correctly use
lookaround regex in Nvim.

# References

+ <https://vi.stackexchange.com/questions/3036/is-lookaround-possible-in-vims-regex-system>
+ [Vim regex cheatsheet](http://vimregex.com/#news)
+ [A good post about vim lookaround regex.](https://jbodah.github.io/blog/2016/11/01/positivenegative-lookaheadlookbehind-vim/)

[^1]: If you are not familiar with what lookaround in regex means, you can find a intro [here](https://www.regular-expressions.info/lookaround.html) and [here](https://stackoverflow.com/questions/2973436/regex-lookahead-lookbehind-and-atomic-groups)
[^2]: Use `:h perl-patterns` to find the difference of Nvim regex and Perl's regex.
