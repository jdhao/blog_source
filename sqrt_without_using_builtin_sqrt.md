---
title: "How to Calculate Square Root without Using sqrt()?"
date: 2022-04-28T21:45:01+08:00
markup: pandoc
tags: [optimization, math]
categories: [Programming]
---

I saw an interesting question that how to get square root of x without using builtin function from your language.
There are different ways to approach this problem.

<!--more-->

# Babylonian method

There is a method called [Babylonian method](https://stackoverflow.com/a/40528142/6064933) to calculate the square root.
See [this Wikipedia page](https://en.wikipedia.org/wiki/Methods_of_computing_square_roots#Babylonian_method) for the details.

The code is as follows:

```python
def sqrt(x):

    if x == 0:
        return 0

    res = x/2.0
    eps = 1e-7

    while res - x/res > eps:
        res = (res + x/res) * 0.5

    return res
```

This method is very fast, it can calculate $sqrt(5)$ is less than 5 iterations.

# Newton method

We can also use [newton's method](https://en.wikipedia.org/wiki/Newton%27s_method) to find the square root of $num$.
Basically, we have the following $f(x)$:

$$f(x) = x^2 - num$$

We can get the square root of $num$ iteratively via:

$$x_{n+1} = x_n - \frac{f(x_n)}{f'(x_n)}$$

The code is like this:

```python
def newton_sqrt(num):
    cur = num / 2.0
    eps = 1e-7

    while abs(get_next(cur, num) - cur) > eps:
        cur = get_next(cur, num)

    return cur


def get_next(x, num):
    return x - f_val(x, num)/df(x)


def f_val(x, num):
    return x*x - num


def df(x):
    return 2*x


def main():
    num = 5
    res = newton_sqrt(num)

    print(f"res: {res}")


if __name__ == "__main__":
    main()
```

Newton's method also works pretty quickly to find the root square of a num.

# Gradient descent

Apart from the Babylonian method, I thought we can also solve this problem with gradient descent easily.

## l1 loss

Suppose that square root of $x$ is $w$, then our loss function will be the difference between $x$ and $w^2$.
Our objective is minimize the loss.

Here is my sample code to solve this problem:

```python
import random

import matplotlib.pyplot as plt
import numpy as np


def main():
    x = 5
    w = random.uniform(1, x)
    print(f"initial w: {w}")

    loss_type = "l2"

    n_iter = 200
    lr = 0.01
    eps = 0.00001
    losses = []

    for i in range(n_iter):
        lr = exp_decay_lr_scheduler(lr, i, n_iter)
        # lr = step_lr_scheduler(lr, i, n_iter)

        if loss_type == 'l1':
            dl_dw = get_dl_dw(w, x)
        else:
            dl_dw = get_dl_dw2(w, x)
        w -= (lr * dl_dw)

        if loss_type == 'l1':
            loss = abs(x - w**2)
        else:
            loss =(x - w**2)**2
        print(f"step: {i}, lr: {lr}, w: {w}, loss: {loss}")
        losses.append(loss)

        if loss < eps:
            break

    print(f"final w: {w}")

    ax = plt.subplot()
    x = range(len(losses))
    ax.plot(x, losses)
    ax.set_xlabel("iteration")
    ax.set_ylabel("loss")

    plt.show()


def step_lr_scheduler(lr, i, n_iter):
    lr_factor = 5
    step = 20

    if i != 0 and i % step == 0:
        lr = lr / lr_factor

    return lr

def exp_decay_lr_scheduler(lr, i, n_iter):
    alpha = 1.0

    return lr * np.exp(-alpha * i / n_iter)


def get_dl_dw(w, x):
    if w*w >= x:
        return 2*w
    else:
        return -2*w

def get_dl_dw2(w, x):
    return 4*w**3 - 4*w*x


if __name__ == "__main__":
    main()
```

There are two points worth noting.

First, it is how we calculate the loss.
Initially I made the mistake of defining the loss as $x - w^2$.
Since we want to minimize the difference between $x$ and $w^2$, the loss should be defined as $\Vert x - w^2 \Vert$.
Otherwise, we can never learn a proper value for $w$.
The derivative of loss w.r.t $w$ is:

$$\frac{\partial l}{\partial w} =
\begin{cases}
      -2w & w^2 < x\\
      2w &  w^2 >= x
\end{cases}$$

Another point is the learning rate schedule.
When $w$ is close to its real value, we should use a small learning rate.
Otherwise, we will see oscillation of the training curve: the loss will bump up and down.
I first tried with step policy for learning rate, i.e., reducing the learning rate after the specified steps.
With this policy, I found that the loss curve will oscillates and is not smooth,
and we can not get a loss significantly small than 0.001.

I tried with the exponentially decaying policy, where we decay the learning rate smoothly with the following factor:

$$\exp(-\alpha * \frac{i}{N})$$

$N$ is the total number of iterations, and $i$ is the current iteration.
With this policy, we can approximate $\sqrt{x}$ with precision as high as $1*10^{-5}$ with fewer iterations.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202204282237723.jpg" width="400">
</p>

## Using l2 loss

Aside from using l1 loss, we can also use l2 loss: $(x - w^2)^2$.
The derivative of loss w.r.t $w$ is:

$$\frac{\partial l}{\partial w} = 4w^3 - 4xw$$

In the above code, we can change `loss_type` to `l2` to try the l2 loss.
Using l2 loss often leads to faster convergence than l1 loss.

# Conclusion

Both the Babylonian method and Newton's method converge much faster than gradient descent.
However, using gradient descent to solve this problem is fun and provides a new perspective.
