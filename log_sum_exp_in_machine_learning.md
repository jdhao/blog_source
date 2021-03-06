---
title: "Why do We Use LogSumExp in Machine Learning?"
date: 2022-01-09T20:39:52+08:00
draft: false
tags: [softmax]
categories: [machine-learning]
markup: pandoc
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202201092324184.jpg" width="600">
</p>

LogSumExp is often used in machine learning. It has the following form:

<!--more-->

\begin{equation}
LSE(x_1, x_2, \ldots, x_n) = \log\sum_{i=1}^{N}\exp(x_i)
\end{equation}

# LSE as an upper bound for max()

LSE is an upper bound for $max(x_1, x_2, \ldots, x_n)$ (The equality establishes only when $n=1$).
We have the following inequality:

\begin{equation}
 max(x_1, x_2, \ldots, x_n) <= \log\sum_{i=1}^{N}\exp(x_i)
\end{equation}

To verify this, I have drawn a graph comparing LSE vs max for 2D data in the range of [-1, 1].
The graph is shown in the title image. It clear that LSE is indeed an uppber bound for max.

<details>
<summary><font size="3" color="red">click here to check the code used to generate the title image</font></summary>

```python
import matplotlib.pyplot as plt

import numpy as np


def main():
    fig = plt.figure()
    ax = fig.add_subplot(projection='3d')

    N = 200
    x1 = np.linspace(-1, 1, N)
    x2 = np.linspace(-1, 1, N)

    X1, X2 = np.meshgrid(x1, x2)
    Y = np.log(np.exp(X1) + np.exp(X2))
    surf = ax.plot_surface(X1, X2, Y, color='red')

    Y_up = np.max(np.stack([X1, X2], axis=0), axis=0)
    surf2 = ax.plot_surface(X1, X2, Y_up, color='blue')

    # y = np.log(np.exp(x1) + np.exp(x2))
    # ax.plot_trisurf(x1, x2, y, color='red')

    # y_up = np.max(np.stack([x1, x2], axis=1), axis=1)
    # ax.plot_trisurf(x1, x2, y_up, color='green')

    ax.set_xlabel("X1")
    ax.set_ylabel("X2")
    ax.set_zlabel("Z")

    # change the 3D plot angel and dist, https://stackoverflow.com/q/12904912/6064933
    ax.view_init(elev=11, azim=-46)
    ax.dist = 10

    # plt.show()

    plt.savefig("log_sum_exp_vs_max.pdf", bbox_inches='tight')


if __name__ == "__main__":
    main()
```
</details>

How is this information useful? Well, we can use it to transform the optimization target.
For example, you may want to optimize $max(x_1, x_2)$, which is not differentiable.
Then we can optimize $LSE(x_1, x_2)$ instead.

In [Lifted structure loss](https://arxiv.org/abs/1511.06452), they used this trick to transform equation 3 to equation 4.
Without this knowledge, you will find it difficult to understand how they arrive at equation 4.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202201100000369.png" width="800">
</p>


# LSE for numerical stability

When we use softmax function to normalize a vector and the vector contains large or small values,
we will encounter numerical issues (overflow or underflow). We need to use LSE to alleviate this issue.

[This post](https://gregorygundersen.com/blog/2020/02/09/log-sum-exp/) explains how it works in detail.


# References

+ https://en.wikipedia.org/wiki/LogSumExp
+ [matplotlib plot_trisurf](https://matplotlib.org/stable/api/_as_gen/mpl_toolkits.mplot3d.axes3d.Axes3D.html?highlight=plot_trisurf#mpl_toolkits.mplot3d.axes3d.Axes3D.plot_trisurf)
