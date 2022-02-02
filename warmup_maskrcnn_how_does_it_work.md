---
title: "Warmup in Maskrcnn-benchmark and how does it work?"
date: 2020-08-14 23:04:54+0800
tags: []
categories: [machine-learning]
---

In [maskrcnn-benchmark](https://github.com/facebookresearch/maskrcnn-benchmark), there is some config parameters about warmup in solver
(`WARMUP_FACTOR`, `WARMUP_ITERS`, `WARMUP_METHOD`). But what is warmup, and how does it work?

<!--more-->

Warmup was originally proposed in this paper: [Accurate, Large Minibatch SGD: Training ImageNet in 1 Hour](https://arxiv.org/abs/1706.02677).
It gives a good explanation on why warmup is needed, and explains different strategies of warmup.

## Why do we need warmup

Suppose that we use learning rate $\eta$ on a single GPU with batch size $n$,
when we train the network on 8 GPUs, now the batch size becomes $8n$.  The
learning rate also needs to change to suit the distributed training scenario.
The author find that in practice, the linear scaling of learning rate works
pretty well. For example, when we use initial learning rate 0.01 for one GPU,
we may use an initial learning rate of 0.08 for distributed training, i.e.,
0.01\*8.

However, to use linear scaling of learning rate, certain condition have to be
met (see section 2.1 of the paper for details). On the initial training stage,
due to the rapid change of network parameters, the condition that makes linear
scaling work does not hold any more. So, in the initial training stage, the
author propose `warmup` to tackle this issue.

The basic idea is that we should use a small learning rate than the value
calculate by linear scaling policy. There are two strategies for warmup:

+ **constant**: Use a low learning rate than 0.08 for the initial few epochs.
+ **gradual**: In the first few epochs, the learning rate is set to be lower
than 0.08 and increased gradually to approach 0.08 as epoch number increases.
In maskrcnn, a `linear` warmup strategy is used for control warmup factor in
the initial learning stage.

After the warmup epochs, the learning rate strategy would return to normal (you
can change the learning rate based on the task at hand).

## How does linear warmup work in maskrcnn

The warmup method used by maskrcnn-benchmark can be found
[here](https://github.com/facebookresearch/maskrcnn-benchmark/blob/master/maskrcnn_benchmark/solver/lr_scheduler.py#L39-L52):

```python
def get_lr(self):
    warmup_factor = 1
    if self.last_epoch < self.warmup_iters:
        if self.warmup_method == "constant":
            warmup_factor = self.warmup_factor
        elif self.warmup_method == "linear":
            alpha = float(self.last_epoch) / self.warmup_iters
            warmup_factor = self.warmup_factor * (1 - alpha) + alpha
    return [
        base_lr
        * warmup_factor
        * self.gamma ** bisect_right(self.milestones, self.last_epoch)
        for base_lr in self.base_lrs
    ]
```

In the above code, `self.last_epoch` is the current training iteration (because
maskrcnn-benchmark use iteration instead of the usual epoch to measure the
training process). `self.warmup_iters` is the number of iterations for warmup
in the initial training stage. `self.warmup_factors` are a constant (0.333 in
this case).

Only when current iteration number is below `self.warmup_iters`, will the
`warmup_factor` be used. Otherwise, it will be 1 and not affect the learning
rate.

When current iteration is below `warmup_iters` and warmup method is `linear`.
The warmup factor used is calculated as follows:

```bash
warmp_factor = 0.667 * (current_iter/warmup_iters) + 0.333
```

So as current iteration approaches `warmup_iters`, `warmup_factor` will
gradually approach 1.  As a result, the learning rate used will approach base
learning rate.

## References

+ [How the learning rate change?](https://github.com/facebookresearch/maskrcnn-benchmark/issues/562)
+ [Discussions about warmup policy](https://www.reddit.com/r/MachineLearning/comments/es9qv7/d_warmup_vs_initially_high_learning_rate/)
