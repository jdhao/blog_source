---
title: "Nonlinear Activations for Neural Networks"
date: 2022-03-27T17:25:12+08:00
draft: false
markup: pandoc
tags: []
categories: [machine-learning]
---

Non-linear activations are important in deep neural networks.
It is important in the sense that without non-linear activation functions, even if you have many linear layers,
the end results is like you have only one linear layer, and the approximation ability of the network is very limited[^1].
Some of most commonly-used nonlinear activation functions are Sigmoid, ReLU and Tanh.

<!--more-->

# Nonlinear activations and their derivatives

## Sigmoid

Sigmoid function, also known as logistic function, has the following form:

$$f(x) = \frac{1}{1+e^{-x}}$$

The derivative of sigmoid is:

$$\frac{df}{dx} = \frac{e^{-x}}{(1+e^{-x})^2} = \frac{1}{1+e^{-x}}(1- \frac{1}{1+e^{-x}}) = f(x)(1-f(x))$$


## Tanh

[Tanh](https://en.wikipedia.org/wiki/Hyperbolic_functions#Exponential_definitions) function

$$f(x) = \frac{e^{2x}-1}{e^{2x}+1}$$

The derivative of Tanh is:

$$\frac{df}{dx} = \frac{4e^{2x}}{(e^{2x} + 1)^2} = 1 - {f(x)}^2$$

## ReLU

ReLU, called rectified linear unit, has the following form:

$$f(x) = \max(0, x)$$

We can also write ReLU as:

$$f(x) =
\begin{cases}
      x & x \geq 0 \\
      0 & x < 0
\end{cases}$$

The derivate of ReLU is quite simple, it is `1` for $x > 0$ and 0 otherwise.

There are also variants of ReLU, such as Leaky ReLU, PReLU (parametric ReLU), and RReLU (randomized ReLU).
In [Empirical Evaluation of Rectified Activations in Convolutional Network](https://arxiv.org/abs/1505.00853),
the author claimed that PReLU and RReLU works better than ReLU in small scale datasets such as CIFAR10, CIFAR100 and [Kaggle NDSB](https://www.kaggle.com/c/datasciencebowl).

# Vanishing gradient

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202203271740705.png" width="800">
</p>

<details>
<summary><font size="2" color="red">Click to show the code for visualization.</font></summary>

```python
import matplotlib.pyplot as plt
import numpy as np


def main():
    x = np.linspace(-5, 5, 100)

    r = [relu(v) for v in x]

    sig = [sigmoid(v) for v in x]
    d_sig = [sigmoid(v)*(1 - sigmoid(v)) for v in x]

    t = [tanh(v) for v in x]
    d_tanh = [1 - tanh(v)**2 for v in x]

    fig = plt.figure(figsize=[6, 3])

    ax = fig.add_subplot(111)

    ax.plot(x, r, '#66c2a5', label='ReLU')

    ax.plot(x, sig, '#fc8d62', label='sigmoid')
    ax.plot(x, d_sig, '#8da0cb', label='sigmoid derivative')

    ax.plot(x, t, '#e78ac3', label='tanh')
    ax.plot(x, d_tanh, '#a6d854', label='tanh derivative')

    ax.legend()

    plt.savefig('activation-curve.png', dpi=96, bbox_inches='tight')


def relu(x):
    if x >=0:
        return x

    return 0


def sigmoid(x):
    return 1/(1 + np.exp(-x))


def tanh(x):
    return (np.exp(x)**2 - 1)/(np.exp(x)**2 + 1)


if __name__ == "__main__":
    main()
```

</details>

The derivative of sigmoid is relatively small, and its largest value is only 0.25 (when $x = 0$).
When $x$ is large, the derivative is near zero.
Tanh has a similar issue: it has a low gradient, and maximum gradient is only 1 ($x=0$).

This will cause the vanishing gradient problem,
because in order to calculate the derivative of loss w.r.t the weight of earlier layers in the network,
we need to multiply the gradient in the later layers.
When you multiply several values below 0.25, the result goes down to zero quickly,
so the network weight in earlier layers get updated slowly.
In other words, the learning process will converge much slower than using ReLU,
and we might need much more epochs to get a satisfactory result.

Another advantage of ReLU is that it is computationally cheap compared to sigmoid,
both in terms of forward and backward operation.

# Demo

To gain more insight into this, we can use [minist on convenet.js](https://cs.stanford.edu/~karpathy/convnetjs/demo/mnist.html) and change the activation function to see how the train goes.
We can see that training process under tanh and sigmoid activation is much slower than ReLU.
Sigmoid is slowest among the three.

We can also play with different activations functions real quick with [TensorFlow playground](https://playground.tensorflow.org/).

# References

+ https://en.wikipedia.org/wiki/Rectifier_(neural_networks)
+ A list of activation functions: https://en.wikipedia.org/wiki/Activation_function
+ ReLU vs Sigmoid: https://stats.stackexchange.com/q/126238/140049

[^1]: See [this post](https://stats.stackexchange.com/a/335972/140049) and also [this one](https://stackoverflow.com/q/9782071/6064933) for more detailed discussions.
