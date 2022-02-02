---
title: "Understanding Computational Graphs in PyTorch"
date: 2017-11-12 13:22:46 +0800
tags: [PyTorch, optimization]
categories: [machine-learning]
---

PyTorch is a relatively new deep learning library which support dynamic
computation graphs. It has gained a lot of attention after its official release
in January. In this post, I want to share what I have learned about the
computation graph in PyTorch. Without basic knowledge of computation graph, we
can hardly understand what is actually happening under the hood when we are
trying to train our _landscape-changing_ neural networks.

<!--more-->

# Computation graphs and its use in PyTorch

The idea of [computation graph](http://colah.github.io/posts/2015-08-Backprop/)
is important in the optimization of large-scale neural networks. In simple
terms, a computation graph is a [DAG](https://stackoverflow.com/q/2283757/6064933)
in which nodes represent variables (tensors, matrix, scalars, etc.) and edge
represent some mathematical operations (for example, summation,
multiplication). The computation graph has some leaf variables. The root
variables of the graph are computed according to operations defined by the
graph. During the optimization step, we combine the chain rule and the graph to
compute the derivative of the output w.r.t the learnable variable in the graph
and update these variables to make the output close to what we want. In neural
networks, these learnable variables are often called weight and bias.

You can also think of neural network as a computational graph: the input images
and the [parameters](http://pytorch.org/docs/master/nn.html#torch.nn.Parameter)
in each layer are leaf variables, the outputs (usually it is called the loss
and we minimize it to update the parameters of the network) of neural networks
are the root variables in the graph.

# How is computation graph created and freed?

In PyTorch, the computation graph is created for each iteration in an epoch. In
each iteration, we execute the forward pass, compute the derivatives of output
w.r.t to the parameters of the network, and update the parameters to fit the
given examples. After doing the backward pass, the graph will be freed to save
memory. In the next iteration, a fresh new graph is created and ready for
back-propagation.

Because the computation graph will be freed by default after the first backward
pass, you will encounter errors if you are trying to do backward on the same
graph the second time. That is why the following error message pops up:

>RuntimeError: Trying to backward through the graph a second time, but the
>buffers have already been freed. Specify retain_graph=True when calling
>backward the first time

# A toy example

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200214143225.png">
</p>

Now, let's take a small example to illustrate the idea. Suppose that we have a
computation graph shown above. The variable `d` and `e` is the output, and `a`
is the input. The underlining computation is:

```python
import torch
from torch.autograd import Variable
a = Variable(torch.rand(1, 4), requires_grad=True)
b = a**2
c = b*2
d = c.mean()
e = c.sum()
```

when we do `d.backward()`, that is fine. After this computation, the part of
graph that calculate `d` will be freed by default to save memory. So if we do
`e.backward()`, the error message will pop up. In order to do `e.backward()`,
we have to set the parameter `retain_graph` to `True` in `d.backward()`, i.e.,

```python
d.backward(retain_graph=True)
```

As long as you use `retain_graph=True` in your backward method, you can do backward any time you want:

```python
d.backward(retain_graph=True) # fine
e.backward(retain_graph=True) # fine
d.backward() # also fine
e.backward() # error will occur!
```

# Real use cases

A real use case that you want to backward through the graph for more than once
is multi-task learning where you have multiple losses at different layers.
Suppose that you have 2 losses: `loss1` and `loss2` and they reside in
different layers. In order to back-prop the gradient of `loss1` and `loss2`
w.r.t to the learnable weight of your network independently. You have to use
`retain_graph=True` in `backward()` method in the first back-propagated loss.

```python
# suppose you first back-propagate loss1, then loss2
# (you can also do it in reverse order)
loss1.backward(retain_graph=True)
loss2.backward() # now the graph is freed, and next process of batch gradient descent is ready

optimizer.step() # update the network parameters
```

# References

+ [leaf variable in PyTorch](https://discuss.pytorch.org/t/leaf-variable-was-used-in-an-inplace-operation/308).
+ [How is computation graph created](https://discuss.pytorch.org/t/which-is-freed-which-is-not/8636/2)
+ [Error trying to backward the second time](https://discuss.pytorch.org/t/runtimeerror-trying-to-backward-through-the-graph-a-second-time-but-the-buffers-have-already-been-freed-specify-retain-graph-true-when-calling-backward-the-first-time/6795).
+ [Understanding graph and state](https://discuss.pytorch.org/t/understanding-graphs-and-state/224).
+ [Graph creation and destroy](https://discuss.pytorch.org/t/how-computation-graph-in-pytorch-is-created-and-freed/3515).
+ [What does `retain_graph` in `backward()` function mean](https://stackoverflow.com/q/46774641/6064933)?
+ [Back-propagation on computation graph](http://colah.github.io/posts/2015-08-Backprop/).
