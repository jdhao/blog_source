---
title: "Similarity Measurement in Image Retrieval"
date: 2017-10-24 20:38:52 +0800
tags: [metric, math, retrieval]
categories: [academic]
markup: pandoc
---

For image retrieval and other similarity-based tasks such as [person re-identification](https://arxiv.org/abs/1610.02984),
we need to compute the similarity (or distance) between the query image and database images.
Then we can rank the database images based on their similarity to the query image.
In this post, I want to briefly introduce two measures widely used in image retrieval tasks.

<!--more-->

# The Euclidean distance

The Euclidean distance is straightforward, suppose $x$ and $y$ are two feature vectors in $\mathbf{R^n}$,
then the Euclidean distance between the two vectors is:

$$\begin{equation}\begin{aligned}
d_{euclid} &= {\Vert x - y \Vert}_2 \\
&= \sqrt{\sum_{i=1}^{n}(x_i - y_i)^2} \\
  &= \sqrt{ {\Vert x \Vert}^2 + {\Vert y \Vert}^2 - 2x\cdot y }\\
\end{aligned}\end{equation}$$

If Euclidean distance between feature vectors of image A and B is smaller than that of image A and C,
we may conclude that image B is more similar to A than image C.

# The cosine similarity

Cosine similarity is another commonly used measure.
For vector $x$ and $y$, it is defined as:

$$\begin{equation}
s = \frac{x\cdot y}{\Vert x \Vert \Vert y \Vert}\ ,
\end{equation}$$

which is actually the cosine value of angle $\theta$ between vector $x$ and $y$.
Here is a plot illustrating that:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/vector_minus.png" width="200">
</p>

How does the above equation come? It can be derived from the [The Law of cosines](https://en.wikipedia.org/wiki/Law_of_cosines).
Based on that law, we have:

$$\begin{equation}
\cos(\theta) = \frac{ {\Vert x \Vert}^2 +{\Vert y \Vert}^2 - {\Vert x - y\Vert}^2 }{2\Vert x \Vert \cdot \Vert y \Vert}\ .
\end{equation}$$

We also have the following equality:

$$\begin{equation}\begin{aligned}
{\Vert x - y\Vert}^2 &= {d_{euclid}}^2 \\
&= {\Vert x \Vert}^2 +{\Vert y \Vert}^2 - 2x\cdot y
\end{aligned}\end{equation}$$

Combining the two equations, we finally get the following equation:

$$\begin{equation}
\cos(\theta) = \frac{x\cdot y}{\Vert x \Vert \cdot \Vert y \Vert}
\end{equation}$$

If the two vectors $x$ and $y$ both are unit vectors, then we can further get:

$$\begin{equation}
\cos(\theta) = x \cdot y
\end{equation}$$

In this case, the cosine similarity between these two vectors equals to their dot product.

# Cosine distance and its relation to Euclidean distance

In image retrieval, the feature vectors are often [$L_2$ normalized](https://stats.stackexchange.com/a/331967/140049) to be a unit vector.
In this case, the Euclidean distance between two vectors $x$ and $y$ becomes:

$$\begin{equation}\begin{aligned}
d_{euclid} &= \sqrt{\sum_{i=1}^{n}(x_i - y_i)^2} \\
  &= \sqrt{ {\Vert x \Vert}^2 + {\Vert y \Vert}^2 - 2x\cdot y }\\
  &= \sqrt{ 2 - 2x\cdot y }\\
  &= \sqrt{ 2(1 - x\cdot y) }\\
\end{aligned}\end{equation}$$

In image retrieval, the feature vector elements are all positive.
$\cos(\theta)$ are in the range $[0, 1]$. Then we can define cosine distance as

$$\begin{equation}
d_{cosine}(x, y) = 1 - x\cdot y \ .
\end{equation}$$

Now it is easy to see that

$$\begin{equation}
d_{euclid}(x, y) = \sqrt{ 2d_{cosine} }\ .
\end{equation}$$

So these two measures are closely related.
You can choose either of the two measurement to assess the similarity between two images.

# References

+ [A good blog post about cosine and Euclidean distance](http://www.yesterdayscoffee.de/2015/12/18/euclidean-and-cosine-distance-for-unit-vectors/)
