---
title: "Creating A Professional Table in LaTeX with booktabs"
date: 2019-08-27 21:31:12+0800
tags: [LaTeX]
categories: [LaTeX]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190827223626.png">
</p>

The default table style provided by LaTeX is not good-looking and professional.
In this post, I would like to talk about how to create professional table with
the help of [booktabs](https://ctan.org/pkg/booktabs?lang=en) package. We will
create the table shown in the title image.

<!--more-->

For how to create a normal table without booktabs, see [this
tutorial](https://www.overleaf.com/learn/latex/Tables). To use booktabs, we
need first to import it since it is not loaded by LaTeX by default:

```latex
\usepackage{booktabs}
```

To [create multi-row cells](https://texfaq.org/FAQ-multirow) in a table, we
need also to include the [multirow](https://ctan.org/pkg/multirow?lang=en)
package:

```latex
\usepackage{multirow}
```

Professional tables often appear as a three-line table, i.e., top line, middle
line and bottom line. The three lines are usually thicker than other lines
that appear in the table. Booktabs package provides the `\toprule`, `\midrule`
and `\bottomrule` command to represent the three lines. To draw a line which
only spans a few columns, you can use `\cmidrule` command.

I show an example to create a table using the booktabs below:

```latex
\begin{table}[h]
    \centering
    \begin{tabular}{lllll}
        \toprule
        \multirow{2}{*}{Models} & \multicolumn{3}{c}{Metric 1} & Metric 2\\
        \cmidrule{2-4} \cmidrule{5-5} \\
        {} & precision & recall & F-score  & R@10 \\
        \midrule
        model 1 & 0.67  & 0.8 & 0.729  & 0.75 \\
        model 2 & 0.8 & 0.9 & 0.847 & 0.85 \\
        \bottomrule
    \end{tabular}
\end{table}
```

The produced table is like:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190827223253.png">
</p>

There are several issues with the table:

+ The text in the multi-row cell is not properly aligned.
+ Row 1 and row 2 are not in the same height.
+ The two `\cmidrule` are connected to each other in one end.

To align the text in multi-row cell vertically, an easy way is to provide a
vertical space parameter before the cell text:

```latex
\multirow{column_num}{cell_width}[vspace]{Text}
```

We can use the `vspace` parameter to adjust the vertical position of `Text`. A
positive value will move the text up while a negative value will move the text
down.

The two `\cmidrule` line are too close to the text in the first row. We can
manually add some space using the `\addlinespace[]` command from the booktabs
package. This command can be put after the table line separator ` \\ ` to add
extra space.

To prevent the two `cmidrule` from connecting each other in one end, we need to
provide the trim parameter to `\cmidurle` command. The trim parameter is
provide in `()` after the command, for example, `\cmidrule(lr){2-4}`, which
means to trim in both left and right side of the rule.

Combining these improvements together, we have the following code:

```latex
\begin{table}[h]
    \centering
    \begin{tabular}{lllll}
        \toprule
        \multirow{2}{*}[-1em]{Models} & \multicolumn{3}{c}{Metric 1} & Metric 2\\
        \addlinespace[10pt]
        \cmidrule(lr){2-4} \cmidrule(lr){5-5} \\
        {} & precision & recall & F-score  & R@10 \\
        \midrule
        model 1 & 0.67  & 0.8 & 0.729  & 0.75 \\
        model 2 & 0.8 & 0.9 & 0.847 & 0.85 \\
        \bottomrule
    \end{tabular}
\end{table}
```
One last issue is that the edge of table is not tight enough: there are
apparent space in the left and right side of the table.
You can add `@{}` in the tabular column setup to trim the extra space like the
following:

```latex
\begin{tabular}{@{}lllll@{}}
```

# References #

+ [Vertical alignment for multirow cell](https://tex.stackexchange.com/a/66599/114857)
+ [Increase row height](https://tex.stackexchange.com/a/262184/114857)
+ [Guide to create tables](https://inf.ethz.ch/personal/markusp/teaching/guides/guide-tables.pdf)
