---
title: "Th Use of Temperature in Softmax"
date: 2022-02-27T12:47:02+08:00
markup: pandoc
draft: false
tags: [softmax]
categories: [machine-learning]
---

Softmax function is commonly used in classification tasks.
Suppose that we have an input vector $[z_1, z_2, \ldots, z_N]$, after softmax, the vector is transformed into a probability distribution:

<!--more-->

$$p_i = \frac{\exp(z_i)}{\sum_{j=1}^{N}\exp(z_j)}$$

In practice, we often see softmax with temperature, which is a slight modification of softmax:

$$p_i = \frac{\exp(x_i/\tau)}{\sum_{j=1}^{N}\exp(x_j/\tau)}$$

The parameter $\tau$ is called the temperature parameter and it is used to control the softness of the probability distribution.
When $\tau$ gets lower, the biggest value in $x$ get more probability,
when $\tau$ gets larger, the probability will be split more evenly on different elements.
Consider the extreme cases.
When $\tau$ approaches zero, the probability for the largest element will approach 1,
while when $\tau$ approaches infinity, the probability for each element will be the same.

```python
import math


def softmax(vec, temperature):
    """
    turn vec into normalized probability
    """
    sum_exp = sum(math.exp(x/temperature) for x in vec)
    return [math.exp(x/temperature)/sum_exp for x in vec]


def main():
    vec = [1, 5, 7, 10]
    ts = [0.1, 1, 10, 100, 10000]

    for t in ts:
        print(t, softmax(vec, t))


if __name__ == "__main__":
    main()
```

With different values of t, the output probability is:

```
0.1 [8.194012623989748e-40, 1.928749847963737e-22, 9.357622968839298e-14, 0.9999999999999064]
1 [0.00011679362893736733, 0.006376716075637758, 0.0471179128098403, 0.9463885774855847]
10 [0.14763314666550595, 0.2202427743860977, 0.26900513210002774, 0.3631189468483686]
100 [0.23827555570657363, 0.24799976560608047, 0.25300969319764466, 0.2607149854897012]
10000 [0.2498812648459304, 0.2499812373450356, 0.2500312385924627, 0.2501062592165714]
```

According to [this post](https://medium.com/@u39kun/is-the-term-softmax-driving-you-nuts-ee232ab4f6bd), the name softmax is kind of misleading,
it should be softargmax, especially when you have a very small $\tau$ value.

For example, for `vec = [1, 5, 7, 10]`, argmax result should be 3.
If we express it as one-hot encoding, the result is `[0, 0, 0, 1]`,
which is pretty close to the result of softmax when $\tau = 0.1$.

# Applications

In [Distilling the Knowledge in a Neural Network](https://arxiv.org/pdf/1503.02531.pdf), they all used temperature parameter in softmax:

> Using a higher value for T produces a softer probability distribution over classes.

In [MoCo](https://arxiv.org/abs/1911.05722), softmax loss with temperature is used (it is a slightly modified version of InfoNCE loss):

$$Loss = -\log\frac{exp(q\cdot k_+/\tau)}{\sum_{i=0}^{K} exp(q\cdot k_i/ \tau)}$$

In that paper, $\tau$ is set to a very small value 0.07.
If we do not use the temperature parameter, suppose that the dot product of negative pairs are -1,
and dot product of positive pair is 1, and we have K = 1024.
In this case, the model has separated the positive and negative pairs perfectly, but the softmax loss is still large:

$$-log\frac{e}{e + 1023e^{-1}} = 4.94$$

If we use a parameter of $\tau = 0.07$, however, the loss will now become literally 0.0.
So using $\tau$ in this case will help collapse the probability distribution to positive pairs and reduce the loss.

MoCo *borrows* this value to [Unsupervised Feature Learning via Non-Parametric Instance Discrimination](https://arxiv.org/pdf/1805.01978.pdf), which says:

> τ is important for supervised feature learning [43], and also necessary for tuning the concentration of v on our unit sphere.

Ref 43 refers to paper [NormFace: L2 Hypersphere Embedding for Face Verification](https://arxiv.org/abs/1704.06369).
In NormFace Sec. 3.3, the authors show theoretically why it is necessary to use a scaling factor[^1] in softmax loss.
Basically, if we do not use a scaling factor, the lower bound for the loss is high,
and we can not learn a good representation of image features.

# References

+ [Why should we use Temperature in softmax?](https://stackoverflow.com/a/63471046/6064933)
+ [What is Temperature in LSTM (and neural networks generally)?](https://cs.stackexchange.com/questions/79241/what-is-temperature-in-lstm-and-neural-networks-generally)
+ https://stats.stackexchange.com/questions/527080/what-is-the-role-of-temperature-in-softmax
+ Understanding the Behaviour of Contrastive Loss: https://arxiv.org/abs/2012.09740
+ https://ogunlao.github.io/2020/04/26/you_dont_really_know_softmax.html
+ https://www.reddit.com/r/MachineLearning/comments/n1qk8w/d_temperature_term_in_simclr_or_moco_papers/

[^1]: In NormFace, they use $s=1/\tau$ as the scaling factor, instead of using $\tau$ directly.
