---
title: "Image or Table Side by Side in LaTeX"
date: 2020-03-07T21:41:38+08:00
draft: false
tags: [LaTeX]
categories: [LaTeX]
---

When we are writing articles using LaTeX, we often need to create side-by-side images or tables. In this post,
I summarize several methods to achieve that.

<!--more-->

When we want to show images or tables side by side, we may have different
requirements. In some situation, we want to combine the images or tables to
illustrate a bigger idea or something. Each sub-image or sub-table has its own
caption and label, and the whole image or table also has its own caption and
label. Other times, we just want to display images or tables side by side to
save space. These images or tables are not subpart of a whole image or table,
i.e., there are independent[^1].

To display image or table side by side. There are several ways:

+ If the images or tables are sub-image or sub-tables: use `subcaption` or `subfig` package.
+ If the images or tables are independent: use `minipage` environment.

# Images or tables not independent

## Use `subcaption`

<details>
<summary><font color="red">Click here to view code.</font></summary>

```latex
\documentclass{article}
\usepackage{graphicx}
\usepackage{subcaption}
\usepackage[capitalize]{cleveref}

\begin{document}

In~\cref{fig:test}, we show two images: \cref{fig:sub1} and \cref{fig:sub2}.

\begin{figure}[h]
    \centering
    \begin{subfigure}[t]{.5\textwidth}
        \centering
        \includegraphics[width=0.9\linewidth]{cat1.jpeg}
        \caption{caption 1}
        \label{fig:sub1}
    \end{subfigure}%
    \begin{subfigure}[t]{.5\textwidth}
        \centering
        \includegraphics[width=0.9\linewidth]{cat2.jpeg}
        \caption{caption 2}
        \label{fig:sub2}
    \end{subfigure}
    \caption{Two cats.}
    \label{fig:test}
\end{figure}

\end{document}
```
</details>

## Use `subfig`

<details>
<summary><font color="red">Click here to view code.</font></summary>

```latex
\documentclass{article}
\usepackage{graphicx}
\usepackage{subfig}
\usepackage[capitalize]{cleveref}

\begin{document}

In~\cref{fig:test}, we show two images: \cref{fig:sub1} and \cref{fig:sub2}.

\begin{figure}[h]
\centering
\subfloat[caption for figure1a]{
    \includegraphics[width=0.4\linewidth]{cat1.jpeg}
    \label{fig:sub1}
}
\subfloat[caption for figure1b]{
\includegraphics[width=0.4\linewidth]{cat2.jpeg}
\label{fig:sub2}
}
\caption{A figure with two subfigure}
\label{fig:test}
\end{figure}

\end{document}
```
</details>


In the following code, the generated caption is below the image. If you want to put
the caption above the image, you need to change an option when importing the subfig package:

```
\usepackage[position=top]{subfig}
```

The caption position of the whole image depends on where you put it.

The above two code snippets are images. For tables, the subcaption package
provides the `subtable` environment; for subfig package, you can still use
`\subfloat` command. Change other code based on the tables, and you should be
fine.

# Images or tables are not independent

If you want to display images or tables side by side, but they are independent, you can use the `minipage`
environment or use `parbox` (only works partially).


## Use `minipage`

<details>
<summary><font color="red">Click here to view code.</font></summary>

```latex
\documentclass{article}
\usepackage{graphicx}
\usepackage{subfig}
\usepackage[capitalize]{cleveref}

\begin{document}

We show two images \cref{fig:sub1} and \cref{fig:sub2} below.

\begin{figure}[h]
    \centering
    \begin{minipage}[t]{.5\textwidth}
        \centering
        \includegraphics[width=0.9\linewidth]{cat1.jpeg}
        \caption{caption 1}
        \label{fig:sub1}
    \end{minipage}%
    \begin{minipage}[t]{.5\textwidth}
        \centering
        \includegraphics[width=0.9\linewidth]{cat2.jpeg}
        \caption{caption 2}
        \label{fig:sub2}
    \end{minipage}
\end{figure}

\end{document}
```
</details>

The code above is easy to understand. The only thing we should notice is the
`%` character between the two minipage environments. This `%` character is
indispensable, which means that the two minipage environment are close to each
other and no space is allowed between them. If there is not `%`, the images
will be displayed in one above the other, not side by side.

Let's take another example to illustrate the use of `%`. Suppose we have a word and we
write it in two separte line, but we do not want to have space between the two parts.
If `%` is not used, there will be space between the two parts. Otherwise, the space
will disappear.

```
impre
ssive

impre%
ssive
```

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200306212220.jpg">
</p>

## Use `parbox`

<details>
<summary><font color="red">Click here to view code.</font></summary>

```latex
\documentclass{article}
\usepackage{graphicx}
\usepackage{subfig}
\usepackage[capitalize]{cleveref}

\begin{document}

We show two images \cref{fig:sub1} and \cref{fig:sub2} below.

\begin{figure}[h]
\parbox{0.5\textwidth}{
    \centering
    \includegraphics[width=0.9\linewidth]{cat1.jpeg}
    \caption{caption 1}
    \label{fig:sub1}
}
\parbox{0.5\textwidth}{
    \centering
    \includegraphics[width=0.9\linewidth]{cat2.jpeg}
    \caption{caption 2}
    \label{fig:sub2}
}
\end{figure}

\end{document}
```
</details>

The parbox command has a drawback: if two images have captions of unequal length or have unequal size, the
two iamges will not be aligned properly. So parbox is not a good choice.

The above two code snippets are for figures. If you need to deal with tables,
change the code related to figures and replace them with the table-related
code, you should be fine.

# References

+ [The use of minipage and subcaption](http://blog.pengyifan.com/the-best-way-to-place-figures-side-by-side-in-latex/).
+ [Minipage and the use of %](http://www.dickimaw-books.com/latex/novices/html/sidebysidefigs.html).
+ [The use % sign](http://www.dickimaw-books.com/latex/novices/html/definitions.html#obj:suppresseol).
+ [Figures side by side](http://tex.stackexchange.com/questions/37581/latex-figures-side-by-side).
+ [Introduction to minipage](http://www.sascha-frank.com/latex-minipage.html).
+ [An introduction to subfig and subcaption package with code](http://texblog.org/2014/01/23/sub-caption-above-subfigures-and-subtables/).
+ [The use of parbox](http://tex.stackexchange.com/questions/2832/how-can-i-have-two-tables-side-by-side).

[^1]: When you refer to these images or tables, the indexes are not shown as 1a, 1b, 1c, but are independent numbers.
