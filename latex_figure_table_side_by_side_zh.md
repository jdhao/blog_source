---
title: "LaTeX 并排显示图像或表格"
date: 2020-03-07T19:46:03+08:00
draft: false
tags: [LaTeX]
categories: [LaTeX]
---

使用 LaTeX 写文章的时候，经常需要并排显示图片或者表格，本文总结几种实现方法。

<!--more-->

当我们把表格或者图片并显示时，我们在不同时候需求是不一样的，有的时候我们要求几张图片或者表格是一个整体的一部分，这些小图片或者表格要有自己的 caption 以及 label；另外的时候，我们只是想并列图片或者表格来节省空间，或者产生对比，这些图片或者表格并不是一个整体，而是毫无关系，相互之间独立的（independent），也就是说这些图片(表格)有自己的 caption 和 label，并且这些 label 是独立的，举例来说，就是这些图片在引用时候，不是以 1a, 1b, 1c 这种形式呈现的，而是单独的数字编号.

对于图像，可以采用两种方式，一种是使用 LaTeX 自带的 minipage 环境，另外一种借助于 subcaption 这个库，还可以使用 subfig 这个库。 如果想要图片或者表格拥有自己的 caption 跟 label，并且整体还有一个 caption 和 label，可以使用 subcaption 与 subfig 这两个库。

# 图片或表格不相互独立

## 使用 subcaption 的写法

<details>
<summary><font color="red">Click to view the code.</font></summary>

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

## 使用 subfig 的写法

<details>
<summary><font color="red">Click to view the code.</font></summary>

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


上述代码，子图的 caption 默认都是在图片下面，如果想要让子图的 caption 在图片上面，需要在添加 subfig 库的时候加上一些选项，`\usepackage[position=top]{subfig}`, 总的图片的 caption 位置则视 caption 放置位置而定。

上述两段代码都是针对图像的，对于表格，subcaption 提供的是 `subtable` 环境，subfig 仍然使用 `\subfloat` 命令，其他的代码针对 table 做一个小修改就可以了，基本不变。

# 图片或表格相互独立

如果想要各个图片或表格只是并列，相互之间独立，可以使用 minipage 环境，或者使用 parbox 也可以部分达成这种目的，

## 使用minipage的写法，

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


上面的源代码很容易看懂，不用太多解释，唯一需要注意的是第一个 minipage 环境结束后的百分号,这个百分号是必不可少的，百分号的意思就是两个 minipage 环境之间不要有空隙，紧密相连，如果没有百分号，编译以后的文档中，两个图片是上下排放的，并不是左右并列。

对百分号再举一个例子，譬如有一个单词，需要分开上下两行，但是不希望隔断，如果不加百分号，两部分中间会有一个空格，加百分号就不会有这种现象了。

```
impre
ssive

impre%
ssive
```

可以看到，空格不见了，这就是百分号的作用。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200306212220.jpg">
</p>

## 使用parbox的写法

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


但是 parbox 有一个缺点，如果两个图片的 caption 的文本数量不一样或者两个图片大小不一致，不能上端对齐或者下端对齐，因而 parbox 并不是一个很好的选项，并不推荐。

上述的两段代码都是针对 figure 的，如果要并排 table，改变跟 figure 有关的代码，换成 table 相关的就可以了，不再赘言。

# 参考资料

+ [讲到了 minipage 与 subcaption 的用法](http://blog.pengyifan.com/the-best-way-to-place-figures-side-by-side-in-latex/).
+ [minipage 环境，对百分号特意说明了](http://www.dickimaw-books.com/latex/novices/html/sidebysidefigs.html).
+ [百分号的用法](http://www.dickimaw-books.com/latex/novices/html/definitions.html#obj:suppresseol).
+ [stackexchange 里面的问答，参考最高票回答](http://tex.stackexchange.com/questions/37581/latex-figures-side-by-side).
+ [minipage 环境的一个详细介绍](http://www.sascha-frank.com/latex-minipage.html).
+ [对 subfig 与 sucaption 环境的一个介绍，并且含有代码](http://texblog.org/2014/01/23/sub-caption-above-subfigures-and-subtables/).
+ [提到了使用 parbox](http://tex.stackexchange.com/questions/2832/how-can-i-have-two-tables-side-by-side).
