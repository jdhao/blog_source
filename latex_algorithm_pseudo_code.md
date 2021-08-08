---
title: "How to Write Algorithm Pseudo Code in LaTeX"
date: 2019-09-21 22:44:19+0800
tags: [algorithms]
categories: [LaTeX]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190921212617.png">
</p>

In this post, I want to summarize what I have learned about creating
algorithmic pseudo code in LaTeX.

<!--more-->

In LaTeX, there are several packages which can help you to write pseudo code,
notably [algorithmicx](https://ctan.org/pkg/algorithmicx?lang=en) and
[algorithm2e](https://ctan.org/pkg/algorithm2e). It seems that algorithm2e is
more actively maintained[^1]. I decided to give algorithm2e a try. The end
result is shown in the title image. The code to create the title image is:

<details>
<summary><font color="red">Click to see the code.</font></summary>

```latex
\documentclass{article}

\usepackage[ruled, lined, linesnumbered, commentsnumbered, longend]{algorithm2e}
\usepackage{xcolor}

\begin{document}

\newcommand\mycommfont[1]{\small\ttfamily\textcolor{blue}{#1}}
\SetCommentSty{mycommfont}

\begin{algorithm}
    \SetKwFunction{isOddNumber}{isOddNumber}
    % \SetKwInput{Input}{Input}
    % \SetKwInput{Output}{Output}
    \SetKwInOut{KwIn}{Input}
    \SetKwInOut{KwOut}{Output}

    \KwIn{A list $[a_i]$, $i=1, 2, \cdots, n$, where each element is an
    integer.}
    \KwOut{Processed list.}

    $newList = [\ ]$

    \tcc{For odd elements in the list, we add 1, and for even elements, we add 2.
    After the loop, all elements are even.}
    \For{$i \leftarrow 0$ \KwTo $n-1$}{
        \eIf{$\isOddNumber(a_i)$}{

            $newList.append(a_i + 1)$ \tcp*[f]{Some thought-provoking comment.}
         }{
            \tcp{Another comment}
            $newList.append(a_i + 2)$
         }
    }

    \KwRet{$newList$}
    \caption{A plain algorithm}
\end{algorithm}
\end{document}
```
</details>

Now I am going to explain the code in detail.

# Controlling the algorithm style

There are certain options for the algorithm2e package which control the
algorithm style. They can be specified when you import the package:

```
\usepackage[ruled, lined, linesnumbered, commentsnumbered, longend]{algorithm2e}
```

The meaning of these options are list below:

+ `ruled`: display algorithm like three-line table.
+ `lined`: display indentation line so that you can clearly see the indentation
    level.
+ `linenumbered`: number the statement line.
+ `commentnumbered`: number the comment line.
+ `longend`: used `end for` to end for loop and use `end if` to end if
    condition.

# Keyword input, output and function

## Define keyword input and output

First we need to specify the input and output of the algorithm. algorithm2e
provides `\KwIn` and `\KwOut` command for input and output respectively:

```
\KwIn{A list $[a_i]$, $i=1, 2, \cdots, n$, where each element is an integer.}
\KwOut{Processed list.}
```

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190921213415.png">
</p>

These keyword parameters are not aligned in its `:` symbol. If you prefer an
aligned keyword, you need to define the input and output keyword yourself like
the following code:

```
\SetKwInOut{KwIn}{Input}
\SetKwInOut{KwOut}{Output}

\KwIn{A list $[a_i]$, $i=1, 2, \cdots, n$, where each element is an
integer.}
\KwOut{Processed list.}
```

In the above code, we redefine the `\KwIn` and `\KwOut` command with
`\SetKwInOut`. `\SetKwInOut` will make sure that the defined keyword command
will algin at the `:` character.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190921213710.png">
</p>

## Define keyword function

You can define a keyword function via `\SetKwFunction{COMMAND}{TEXT}` command.

`COMMAND` is used to define the keyword function command, and `TEXT` is the
text that will be shown when you invoke the command, for example:

```
\SetKwFunction{isOddNumber}{isOddNumber}
```

Then you can invoke the command via `\isOddNumber`.

# Comment

By default, the comment used by algorithm2e has its default style (font size,
character color, etc.). If you are not satisfied with comment style, you can
change it via `\SetCommentSty{}` command. For example, the following code, we
change the comment font size, font style and color:

```
\usepackage{xcolor}
\newcommand\mycommfont[1]{\small\ttfamily\textcolor{blue}{#1}}
\SetCommentSty{mycommfont}
```

Algorithm2e provides `\tcc` and `\tcp` command to create multi-line and single
line C-style comments respectively.

For example, you can use `\tcc` to create a multi-line comment:

```
\tcc{For odd elments in the list, we add 1, and for even elments, we add 2.
After the loop, all elements are even.}
```

The created comment looks like:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190921213913.png">
</p>

You can use `\tcp` to create single line comment. You can put `\tcp{}` command
on a separate line to the statement you want to comment:

```
\tcp{Another comment}
$newList.append(a_i + 2)$
```

The produced comment is like:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190921215351.png">
</p>

When you want to put `\tcp` in the same line with the statement, there is also
a stared version `\tcp*[OPTION] {}` with options. The options is used to change
the comment style slightly. For example, the following code:

```
$newList.append(a_i + 1)$ \tcp*[f]{Some thought-provoking comment.}
```

will create inline comment shown below.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190921214452.png">
</p>

# References

+ [Create pseudo code in LaTeX](http://shantoroy.com/latex/how-to-write-algorithm-in-latex/).
+ [How to un-align keyword commands](https://tex.stackexchange.com/q/367274/114857).
+ [Remove end in if block](https://tex.stackexchange.com/q/185356/114857).

[^1]: More discussions about different packages to produce pseudo in LaTeX can be found [here](https://tex.stackexchange.com/q/229355/114857) and [here](https://en.wikibooks.org/wiki/LaTeX/Algorithms).
