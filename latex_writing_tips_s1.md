---
title: "Tips on Writing Papers in LaTeX"
date: 2019-08-28 14:36:31+0800
tags: [LaTeX, writing]
categories: [LaTeX]
---

In this post, I want to share some tips on writing papers using $\LaTeX$.

<!--more-->

# Use non-breaking space before references #

In certain cases, two piece of texts must be close to each other and not appear
in different lines, e.g., when you refer to an image or a table. This is when
[non-breakable space](https://en.wikibooks.org/wiki/LaTeX/FAQ#Non-breaking_spaces)
comes to help. In LaTeX, you can insert a non-breakable space with `~`. For
example, to refer to some figure you have defined, you can use the following:

```
The figure~\ref{fig:some_fig} shows an interesting result.
```

This will make sure that `figure` will always be followed by the index number
in the same line.

# Period (or dot) and sentence spacing #

LaTeX use different width of space between words in a sentence and between
two sentences. Typically, a lower case letter followed by a dot ends a
sentence. An upper case letter followed by a dot is some kind of an acronym and
is not considered end of sentence. When these rules are broken, you need to
tell LaTeX whether there is an inter-word space or a inter-sentence space.

If a dot follows a lower case letter and it is not the end of a sentence, you
should follow the dot with an inter-word space (`\ `):

```
The book is first published in the Combin.\ Journal.
```

For phrases such as `Prof. Adam`, a non-breakable space is more appropriate:

```
Prof.~Adam
```

If a dot follows an upper case letter and it is indeed the end of a sentence,
you should precede the dot with `\@`:

```
WHO is a world orgnization, just like WTO\@.
```

# Do not use `\textit` for emphasizing text #

Some people like to use `\textit` for emphasizing text. Although this works
most of the time, it is not encouraged. For example, what if you want to
emphasize text inside `\textit` command? To emphasize text, you should use
`\emph{}` which is dedicated to do this job and does it well.

# Terminate command with curly braces #

If you use `chktex`, you may see warnings like the following:

> command terminated with space

That is because if a command does not terminate with a space, the space after
it may be ignored by LaTeX.

```
Good: \LaTeX{} is great.
Bad: \LaTeX is great.
```

# Space and parentheses

In short, spaces before the opening parentheses is necessary. If you do not
need comma or dot, space after closing parentheses is also necessary. In the
enclosed text, there is no need to add spaces in the two ends.

```
national basketball association (NBA): ✔️
national basketball association(NBA): ❌
national basketball association ( NBA ): ❌
```

# Quotation marks #

If you use two `"` for double quotation and two `'` for single quotation, you
will find the opening quotation is apparently wrong. The correct way to
represent double quotation is ``` ``some text'' ```.
The correct way to use single quotation is `` `some text' ``.

# References #

+ [When should we use non-breakable space](https://tex.stackexchange.com/questions/15547/when-should-i-use-non-breaking-space?rq=1)
+ [Inter-sentence spacing in LaTeX](https://tex.stackexchange.com/questions/55105/when-should-i-use-intersentence-spacing)
+ [Sentence spacing](https://stackoverflow.com/a/2024341/6064933)
+ [Command terminated with space warning](https://tex.stackexchange.com/questions/392168/why-does-chktex-lint-command-terminated-with-space)
+ <https://tug.org/pipermail/texhax/2017-April/022640.html>
+ [When to use space around parentheses](https://english.stackexchange.com/questions/5987/is-there-any-rule-for-the-placement-of-space-after-and-before-parentheses)
+ [Quotation marks in LaTeX](https://tex.stackexchange.com/questions/531/what-is-the-best-way-to-use-quotation-mark-glyphs)
