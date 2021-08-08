---
title: LaTeX 中参考文献管理以及注意事项
date: 2015-10-16 14:56:00 +0800
tags: [bibliography, LaTeX]
categories: [LaTeX]
---

LaTeX 写作中，参考文献一般单独放在一个 `*.bib` 文件中，每个文章对应该文件的一个
条目，每个文章有一个独一无二的 Key，在文章中使用 key 来引用对应的文章。
`bibtex` 可以根据 `*.bib` 文件产生 LaTeX 可以识别的引用条目，然后我们才可以在
LaTeX 中引用相关的文献。 本文主要写一些和文献管理以及引用相关的注意事项。

<!--more-->

# 关于多个作者的姓名如何正确分开

一般来说，如果一篇文章有多个作者，作者姓名之间直接用 `and` 连接，作者姓名按照
`first_name last_name` 或者 `first_name middle_name last_name` 来写即可，如果一
个作者名字不是这种形式，那么需要用花括号把作者的 `last_name`括起来，以便 bibtex
程序能够正确识别作者姓名。例如，

```
San Zhang and Si Li and Ma Zi Wang
G{\”o}del
Jose {Dos Santos}
```

# 引用的文字与文献之间用 ~ (tilde) 连接

为了防止 LaTeX 把文字和对应的引文编号排版在两行，文字和对应的引文之间要使用 `~`
符号，这在 LaTeX 中被称为 [unbreakable
space](https://tex.stackexchange.com/questions/15547/when-should-i-use-non-breaking-space)
，举例如下，

```
Zhang et al.~\cite{zhangsan2010cvpr}
```

另外，在引用 Figure，table，equation 等内容是也要使用 unbreakable space，例如

```
Fig.~\ref{fig:foo1}
Table~\ref{tab:bar1}
Eqn.~\ref{eq:foo1}
```

# 为引用的文献选择正确的 entry type 以及 field 名称

不同的参考文献有不同的类型，我们必须为参考文献指定正确的类型，例如 `article`,
`conference` 等。同时，不同类型可能需要不同的域（field）。

## 常用的参考文献的类型以及含义

```
@article :An article from a journal or magazine
@inproceedings: An article in a conference proceedings
@conference: The same as inproceedings
@book: A book with an explicit publisher
@booklet: A work that is printed and bound, but without a named publisher or sponsoring institution
@inbook: A part of a book, which may be a chapter (or section or whatever) and/or a range of pages
@incollection: A part of a book having its own title
@manual: Technical documentation
@mastersthesis: A Master's thesis
@misc: Use this type when nothing else fits
@phdthesis: A PhD thesis
@proceedings: The proceedings of a conference
@techreport: A report published by a school or other institution, usually numbered within a series
@unpublished: A document having an author and title, but not formally published
```

## field 类型

常用的 field 类型以及说明如下

```
author
The name(s) of the author(s), in the format described in the LaTeX book.

title
The work's title, typed as explained in the LaTeX book.

booktitle
Title of a book, part of which is being cited. See the LaTeX book for how to type titles. For book entries, use the title field instead.

chapter
A chapter (or section or whatever) number.

journal
A journal name. Abbreviations are provided for many journals.

year
The year of publication or, for an unpublished work, the year it was written. Generally it should consist of four numerals, such as 1984, although the standard styles can handle any year whose last four nonpunctuation characters are numerals, such as `\hbox{(about 1984)}'.

volume
The volume of a journal or multi-volume book.

howpublished
How something strange has been published. The first word should be capitalized.

edition
The edition of a book---for example, ``Second''. This should be an ordinal, and should have the first letter capitalized, as shown here; the standard styles convert to lower case when necessary.

institution
The sponsoring institution of a technical report.

key
Used for alphabetizing, cross referencing, and creating a label when the ``author'' information is missing. This field should not be confused with the key that appears in the cite command and at the beginning of the database entry.

month
The month in which the work was published or, for an unpublished work, in which it was written. You should use the standard three-letter abbreviation, as described in Appendix B.1.3 of the LaTeX book.

number
The number of a journal, magazine, technical report, or of a work in a series. An issue of a journal or magazine is usually identified by its volume and number; the organization that issues a technical report usually gives it a number; and sometimes books are given numbers in a named series.
organization
The organization that sponsors a conference or that publishes a manual.

pages
One or more page numbers or range of numbers, such as 42--111 or 7,41,73--97 or 43+ (the `+' in this last example indicates pages following that don't form a simple range). To make it easier to maintain Scribe-compatible databases, the standard styles convert a single dash (as in 7-33) to the double dash used in TeX to denote number ranges (as in 7--33).

school
The name of the school where a thesis was written.

series
The name of a series or set of books. When citing an entire book, the the title field gives its title and an optional series field gives the name of a series or multi-volume set in which the book is published.
```

## field 与 entry 的名称都是 case insensitive

field 与 entry 的名称都是 case insensitive，因此 `author`，`Author`，`AUTHOR`之
间没有区别。

# title 中保持某些字符的大小写状态

title 中的字母大小写对最终输出是没有影响的，大小写等格式由参考文献的风格来决定
[^1]，因此如果想要保持 title 中某些字母的大写，需要用花括号把字母括起来。如：

```
title = "Proof of the {R}iemann {H}ypothesis"
```

---

# 参考

+ 书写作者姓名，<http://nwalsh.com/tex/texhelp/bibtx-23.html>
+ <https://faculty.math.illinois.edu/~hildebr/tex/bibliographies0.html>
+ <https://faculty.math.illinois.edu/~hildebr/tex/bibliographies.html>
+ LaTeX primer, section IV, <https://www.tug.org/twg/mactex/tutorials/ltxprimer-1.0.pdf>
+ Entry type 的含义，<https://nwalsh.com/tex/texhelp/bibtx-7.html>

[^1]: 在 LaTeX 中通过 `\bibliographystyle{}` 命令来设定具体使用哪种参考文献风格。
