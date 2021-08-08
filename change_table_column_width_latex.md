---
title: "Change Table Column Width in LaTeX"
date: 2020-03-08T12:05:37+08:00
draft: false
tags: [LaTeX]
categories: [LaTeX]
---

When we [create tables in LaTeX](https://jdhao.github.io/2019/08/27/latex_table_with_booktabs/),
the table column width will be automatically decided based on the content in
the table cell. Sometimes, maybe we are not satisfied with the default column
width and want to customize the column width. In this post, we will explain how
to it in LaTeX.

<!--more-->

Our initial table is like the following:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200308141219.jpg" width="400">
</p>

The code to produce this table is:

<details>
<summary><font size="2" color="red">Click to show the code.</font></summary>

```latex
\documentclass{article}
\usepackage{booktabs}
\usepackage{multirow}

\begin{document}

\begin{table}[t]
    \centering
    \begin{tabular}{llll}
        \toprule
        \multirow{2}{*}{Country} & \multicolumn{2}{c}{GDP} & Change \\
        \cmidrule{2-3} \\
        {} & 2010 & 2015 & {} \\
        \midrule
        United States & 14964.400 & 18036.650 & 20.53\% \\
        China & 5812.464 & 11226.186 & 93.14\% \\
        Japan & 5793.455 & 4382.420 & -24.36\% \\
        Germany & 3309.668 & 3365.293 & 1.68\% \\
        United Kingdom & 2246.079 & 2863.304 & 27.48\% \\
        \bottomrule
    \end{tabular}
\end{table}

\end{document}
```
</details>

# Column type with width parameter

Column width of the produced table is small. To control the column width, we need to use
another three column types `p`, `m`, `b`:

| Column type | Description                                     |
|-------------|-------------------------------------------------|
| p{width}    | Create fixed width column with top alignment    |
| m{width}    | Create fixed width column with middle alignment |
| b{width}    | Create fixed width column with bottom alignment |

The `width` parameter can be absolute value, like `2cm`, `20pt` etc. It can
also be a relative value, e.g., `0.2\textwidth`. Using relative width is
preferred since the table width are often measured relative to `\textwidth`.

# Table cell horizontal alignment

The above three column types only specify the vertical alignment of table
column cell. To specify the horizontal alignment of table cell, we need to use
the following three command provided by the `array` package:

| Alignment      | Description      |
|----------------|------------------|
| `\centering`   | Center alignment |
| `\raggedright` | Left alignment   |
| `\raggedleft ` | Right alignment  |

To add the alignment command to each cell, thus adjusting the column cell
horizontal alignment, we use the following syntax before the table column type
declaration:

```
>{some_command}
```

This will ensure that `some_command` is inserted before every column cell
content.

# Table with wider column and custom alignment

To use bigger column width and center alignment in first and last column in the
above table, we can use the following code:

<details>
<summary><font size="2" color="red">Click to show the code.</font></summary>

```latex
\documentclass{article}
\usepackage{booktabs}
\usepackage{multirow}
\usepackage{array}

\begin{document}

\begin{table}[t]
    \centering
    \begin{tabular}{>{\centering}p{0.25\textwidth}p{0.2\textwidth}p{0.2\textwidth}>{\centering\arraybackslash}p{0.15\textwidth}}
        \toprule
        \multirow{2}{*}{Country} & \multicolumn{2}{c}{GDP} & Change \\
        \cmidrule{2-3} \\
        {} & 2010 & 2015 & {} \\
        \midrule
        United States & 14964.400 & 18036.650 & 20.53\% \\
        China & 5812.464 & 11226.186 & 93.14\% \\
        Japan & 5793.455 & 4382.420 & -24.36\% \\
        Germany & 3309.668 & 3365.293 & 1.68\% \\
        United Kingdom & 2246.079 & 2863.304 & 27.48\% \\
        \bottomrule
    \end{tabular}
\end{table}

\end{document}
```
</details>


For table cells where the horizontal alignment is not explictly defined, the
default is left alignment. Note that if you want to change the alignment of
**last column**, the `\arraybackslash` command **must** also be supplied, or
you will encounter errors when compiling the LaTeX source code:

> Extra alignment tab has been changed to \cr.

The reason of this error can be found [here](https://texfaq.org/FAQ-altabcr) if
you are interested.

The created table will be like the following:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200308144401.jpg" width="400">
</p>

# Defining new column types to simplify things

Writing column width and alignment in the tabular column type option is rather
lengthy and error-prone. We can define new column types and use them in the
column type option to simplify things.

The `\newcolumntype` command from `array` package is used to define new column
types, for example:

```
\newcolumntype{A}{>{\centering}p{0.25\textwidth}}
```

Using this command, our code now becomes more concise and readable:

<details>
<summary><font size="2" color="red">Click to show the code.</font></summary>

```LaTeX

\documentclass{article}
\usepackage{booktabs}
\usepackage{multirow}
\usepackage{array}

\begin{document}

\newcolumntype{A}{>{\centering}p{0.25\textwidth}}
\newcolumntype{B}{p{0.2\textwidth}}
\newcolumntype{C}{>{\centering\arraybackslash}p{0.15\textwidth}}
\begin{table}[t]
    \centering
    \begin{tabular}{ABBC}
        \toprule
        \multirow{2}{*}{Country} & \multicolumn{2}{c}{GDP} & Change \\
        \cmidrule{2-3} \\
        {} & 2010 & 2015 & {} \\
        \midrule
        United States & 14964.400 & 18036.650 & 20.53\% \\
        China & 5812.464 & 11226.186 & 93.14\% \\
        Japan & 5793.455 & 4382.420 & -24.36\% \\
        Germany & 3309.668 & 3365.293 & 1.68\% \\
        United Kingdom & 2246.079 & 2863.304 & 27.48\% \\
        \bottomrule
    \end{tabular}
\end{table}

\end{document}
```
</details>

# References

+ [Control column width](https://texblog.org/2019/06/03/control-the-width-of-table-columns-tabular-in-latex/).
+ [World GDP data](https://en.wikipedia.org/wiki/List_of_countries_by_largest_historical_GDP).
+ [`\newcolumntype` usage](https://tex.stackexchange.com/questions/257128/how-does-the-newcolumntype-command-work).
