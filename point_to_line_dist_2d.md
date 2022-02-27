---
title: "Point to Line Distance in 2D Plane"
date: 2020-02-23T18:36:27+08:00
draft: false
tags: [math]
categories: [Note]
markup: pandoc
---

Suppose that we have a straight line formed by point B and C, and we have another
point A. What is the distance from point A to line BC?

<!--more-->

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200223201931.jpg" width="400">
</p>

There are various ways to calculate this distance. Using vector calculus is one
of the easiest ways to do it.

From elementary math, we know that distance from A to line BC is the distance
between point A and D, where D is a point in line BC and line AD is
perpendicular to line BC.

So the problem really boils down to finding the distance between between point
A and D. That is, we need to find the value of following equations:

\begin{equation}
\Vert \overrightarrow{AD} \Vert = \sqrt{(D_x - A_x)^2 + (D_y - A_y)^2}
\end{equation}

Since point D is in line BC, so vector $\overrightarrow{BD}$ and vector
$\overrightarrow{DC}$ is parallel to each other, i.e., $\overrightarrow{BD} =
t\cdot \overrightarrow{DC}$. We also know that $\overrightarrow{AD}$ is perpendicular
to $\overrightarrow{BC}$. From these two conditions, we have the following two
equations:

\begin{gather}
\frac{D_x - B_x}{C_x - D_x} = \frac{D_y - B_y}{C_y - D_y} \label{eq2}  \\
(D_x - A_x)(C_x - B_x) + (D_y - A_y)(C_y - B_y) = 0 \label{eq3}
\end{gather}

Simplifying $\eqref{eq2}$, we get:

\begin{equation}
(D_x - B_x)(C_y - D_y) - (D_y - B_y)(C_x - D_x) = 0 \label{eq4}
\end{equation}

Transforming $\eqref{eq4}$ a bit, and we get:

\begin{equation}
\begin{split}
[(D_x - A_x) + (A_x - B_x)][(A_y - D_y) + (C_y - A_y)] -\\ [(D_y-A_y) + (A_y-B_y)][(A_x-D_x) + (C_x - A_x)] = 0
\end{split}\label{eq5}
\end{equation}

Now, to simplify calculation, we use the following substitution:

\begin{align}
D_x - A_x &= m \label{eq6} \\
D_y - A_y &= n \label{eq7}
\end{align}

Substitute `m` and `n` into equation $\eqref{eq3}$ and $\eqref{eq5}$, and
simplifying a bit, we now get:

\begin{gather}
m(C_x-B_x) + n(C_y-B_y) = 0 \label{eq8}  \\
m(C_y-B_y) - n(C_x - B_x) = (A_y - B_y)(C_x-A_x) - (A_x - B_x)(C_y-A_y) \label{eq9}
\end{gather}

From equation $\eqref{eq8}$ and $\eqref{eq9}$, it it now easy to derive the value of
$m$ and $n$:

\begin{align}
m &= - \frac{(C_y - B_y)[A_y(B_x - C_x) - B_y(A_x - C_x) + C_y(A_x - B_x)]}{(C_x - B_x)^2 + (C_y - B_y)^2}\\
n &= \frac{(C_x - B_x)[A_y(B_x - C_x) - B_y(A_x - C_x) + C_y(A_x - B_x)]}{(C_x - B_x)^2 + (C_y - B_y)^2}\\
\end{align}

So the square sum of $m$ and $n$ is:

\begin{equation}
m^2 + n^2 = \frac{[A_y(B_x - C_x) - B_y(A_x - C_x), + C_y(A_x - B_x)]^2}{(C_x - B_x)^2 + (C_y - B_y)^2}
\end{equation}

The norm of vector $\overrightarrow{AD}$ is:

\begin{equation}
\Vert \overrightarrow{AD} \Vert = \frac{\lVert A_y(B_x - C_x) - B_y(A_x - C_x), + C_y(A_x - B_x)\rVert}{\sqrt{(C_x - B_x)^2 + (C_y - B_y)^2}}
\end{equation}

In the above equation, the denominator is the norm of vector
$\overrightarrow{BC}$, the nominator is the norm of [cross
product](https://en.wikipedia.org/wiki/Cross_product) between
$\overrightarrow{BC}$ and $\overrightarrow{BA}$.

So the distance from point A to line BC can also be written as

\begin{equation}
\lVert \overrightarrow{AD} \rVert = \frac{\lVert \overrightarrow{BC} \times \overrightarrow{BA} \rVert}{\lVert \overrightarrow{BC} \rVert}
\end{equation}

# References

+ https://en.wikipedia.org/wiki/Distance_from_a_point_to_a_line
+ https://stackoverflow.com/q/39840030/6064933
+ https://math.stackexchange.com/questions/330269/the-distance-from-a-point-to-a-line-segment
