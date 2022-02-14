---
title: "Why Use Cross Entropy in Classification Task?"
date: 2021-10-16T09:54:49+08:00
draft: false
tags: [loss, softmax]
categories: [academic]
---

In classification tasks, the de facto loss to use is the [cross entropy loss](https://pytorch.org/docs/stable/generated/torch.nn.CrossEntropyLoss.html).

<!--more-->

Suppose that we have 10 classes, we would like the network to predict the
probabilities of current sample belonging to each of the 10 classes. However,
the raw output from from a neural network is just floating point values. So the
[softmax function](https://en.wikipedia.org/wiki/Softmax_function) is used to normalize the output to fall in the range $(0, 1)$.

After softmax, all output values are between 0 and 1 and their sum is 1. So the
output now can be considered as the probability distribution over the predicted
classes. The element with largest probability is the predicted class.

Now, suppose we have a batch of N data samples and their class labels, from the
point of maximum likelihood estimation (or [MLE](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation) in short), we want to find
the parameters of the neural network that can maximize the product of
probabilities that each sample get in their ground truth class.

For example, if we have data sample x1, x2 and x3, and their class label is 1,
3, 5. Then we want to find network parameters that can maximize `p{11} * p{23} * p{35}`.

Then according to theory of MLE, we need to do derivative stuff and find the
parameter. However, the multiplication form is not suitable for calculating
derivatives. That is why `log()` function used in this.

<!-- should include log curve here-->

Why log function? Because:

1. log function is monotonic. Or our objective is the same. Maximizing the old
objective is equivalent to maximizing the new objective.
2. log(x * y) = log(x) + log(y), so that we can greatly simplify calculation of derivatives.

Another question, why do we use the minus sign?

Because in machine learning, we always talk about minimizing the loss/cost,
which is equivalent to maximize log likelihood. It is just a convention. By
adding a minus sign, we transform the initial problem of maximizing likelihood
to minimize the new loss function. They are essentially the same, but loss
function is a more familiar jargon to machine learning practitioners.

The cross entropy loss is also called log loss.

To be continued...

# References

+ https://stats.stackexchange.com/questions/436766/cross-entropy-with-log-softmax-activation
+ https://stats.stackexchange.com/questions/366312/why-we-use-log-function-for-cross-entropy
+ https://stats.stackexchange.com/questions/222585/what-are-the-impacts-of-choosing-different-loss-functions-in-classification-to-a
+ [On Loss Functions for Deep Neural Networks in Classification](https://arxiv.org/abs/1702.05659)
+ https://paperswithcode.com/paper/demystifying-loss-functions-for
+ https://machinelearningmastery.com/how-to-choose-loss-functions-when-training-deep-learning-neural-networks/
