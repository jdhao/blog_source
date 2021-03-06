---
title: "Some Loss Functions and Their Intuitive Explanations"
date: 2017-03-13 10:14:55 +0800
tags: [loss, softmax]
categories: [academic]
markup: pandoc
---

Loss functions are frequently used in supervised machine learning to minimize
the differences between the predicted output of the model and the ground truth
labels. In other words, it is used to measure how good our model can predict
the true class of a sample from the dataset. Here I would like to list some
frequently-used loss functions and give my intuitive explanation.

<!--more-->

# Cross Entropy Loss

[Cross Entropy Loss](https://en.wikipedia.org/wiki/Cross_entropy) is usually
used in classification problems. In essence, it is a measure of difference
between the desired probablity distribution and the predicted probablity
distribution. Suppose the classification is binary classification problem, the
label are ${0, 1}$. Then the loss function for a single sample in the dataset
is expressed as:

$$-y \log(p)-(1-y) \log(1-p)\ ,$$

where $y$ is the label of the sample, and $p$ is the predicted probability of
the sample belonging to class 1.

For $K$-class ($K >2$ ) classification problems, the predicted probablity
output for a single sample is a vector of length $K$: $[p_0, p_1, \ldots,
p_{K-1}]$. The Cross Entropy Loss is extended as: $$-log (p_k)\ ,$$ where $k$
is the ground truth label for the sample. If $p_k$ equals 1, then there is no
loss incurred for that sample.

We often see the term "Softmax Loss" in literature or blog post. In fact, there
is no such thing as "Softmax Loss", as is discussed [on this Quora
post](https://www.quora.com/Is-the-softmax-loss-the-same-as-the-cross-entropy-loss).
Suppose the original prediction for a sample is $[x_0, x_1, \ldots, x_{K-1}]$,
we can get the normalized probablity $[p_0, p_1, \ldots, p_{K-1}]$， where we
have:

$$p_k = \frac{\exp(x_k)}{\sum_{i=0}^{K-1}\exp(x_i)}\ .$$

So the Cross Entropy Loss really is:

$$-\log \frac{\exp(x_k)}{\sum_{i=0}^{K-1}\exp(x_i)}\ .$$

In brief, the so-called Softmax Loss is just Softmax function followed by Cross
Entropy Loss.

# Contrastive Loss

Contrastive Loss is often used in image retrieval tasks to learn discriminative
features for images. During training, an image pair is fed into the model with
their ground truth relationship $y$: $y$ equals 1 if the two images are similar
and 0 otherwise. The loss function for a single pair is:

$$yd^2+(1-y)\max (margin-d, 0)^2\ ,$$

where $d$ is the Euclidean distance between the two image features (suppose
their features are $f_1$ and $f_2$): $d = \Vert f_1 - f_2\Vert_2$.  The
$margin$ term is used to "tighten" the constraint: if two images in a pair are
dissimilar, then their distance shoud be at least $margin$, or a loss will be
incurred.

# Triplet Loss

Triplet Loss is another loss commonly used in CNN-based image retrieval. During
training process, an image triplet $(I_a, I_n, I_p)$ is fed into the model as a
single sample, where $I_a$, $I_n$ and $I_p$ represent the anchor, postive and
negative images respectively. The idea behind is that distance between anchor
and positive images should be smaller than that between anchor and negative
images. The formal definition is:

$$\max \left( {\Vert f_a- f_p \Vert}^2 - {\Vert f_a - f_n \Vert}^2 + m, 0\right)\ .$$

In the above equation, $m$ is a margin term used to "stretch" the distance
differences between similar and dissimilar pair in the triplet, $f_a, f_p, f_n$
are the feature embeddings for the anchor, postive and negative images.

---

# References

+ [Contrastive Loss](http://docs.chainer.org/en/stable/reference/functions.html#chainer.functions.contrastive)
+ [Triplet Loss](http://docs.chainer.org/en/stable/reference/functions.html#chainer.functions.triplet)
+ [Cross Entropy Loss](https://rdipietro.github.io/friendly-intro-to-cross-entropy-loss/)
+ [A lot more loss functions in machine learning](http://christopher5106.github.io/deep/learning/2016/09/16/about-loss-functions-multinomial-logistic-logarithm-cross-entropy-square-errors-euclidian-absolute-frobenius-hinge.html)
