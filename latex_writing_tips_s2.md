---
title: "Tips on Writing Papers in LaTeX"
date: 2020-03-06T18:42:18+08:00
draft: false
tags: [LaTeX, writing]
categories: [LaTeX]
---

Some useful tips on writing papers in LaTeX.

<!--more-->

## Use package `cleveref` for easier reference.

If you use the LaTeX builtin command `\ref{}` to refer to a table or image, it
will only give you the index of referred object. You have to manually type ref
type, i.e., `Fig.` or `Table`. The package
[`cleveref`](https://ctan.org/pkg/cleveref?lang=en) will help you to insert the
reference type automatically. It provides the `\cref` (used inside a sentence)
and `\Cref` (used at the beginning of sentence) command to refer to an object
easily. `\cref` can also be used to refer to multiple labels like this:

```
% no space allowed between different labels
\cref{label1,label2,label3}
```

Add the following setting to your header in order to use `cleveref`:

```latex
\usepackage[capitalise]{cleveref}
% for cleveref
\newcommand{\crefrangeconjunction}{--}
```

In the above command, we modify the `\crefrangeconjunction` to `--`, since the
default is to use `to` between a range of references.

## Use punctuations after equations.

Usually we will show some equations in the paper to show the formal
formulation. Equations are like text and should be given proper punctuation. If
it is in the middle of an sentence, use a comma. If it is in the end of a
sentence, use a period. For example,

```
Einstein propose the famous matter-energy equation:
\begin{equation}
E = mc^{2}.
\end{equation}
```

## How to reduce lines

+ You should use the [`microtype`](https://ctan.org/pkg/microtype?lang=en) package.
It can help to adjust the word spacing to reduce the lines taken. You will
often see less underfull or overfull hbox warning after using this package.

+ You shouldn't add blank lines before and after the display style equations if
the text before and after the equation belong to the same paragraph. This
will also help to reduce space.

## References

+ Punctutation:
	+ https://tex.stackexchange.com/q/7542/114857
	+ https://tex.stackexchange.com/q/32853/114857
	+ https://mathoverflow.net/questions/6675/periods-and-commas-in-mathematical-writing
+ microtype:
	+ https://tex.stackexchange.com/questions/66052/should-i-load-microtype-with-pdflatex
	+ http://texblog.net/latex-archive/latex-general/pdflatex-microtype/
	+ http://www.khirevich.com/latex/microtype/
+ https://docs.google.com/document/d/1zXXtti9JgcTERvIZXNwSRncw2T1dUC8dt6PJlYSH-2g/edit#heading=h.zf43ulrlas66
