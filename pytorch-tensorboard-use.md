---
title: "How to Use Tensorboard in Pytorch"
date: 2022-04-20T20:53:51+08:00
markup: markdown
tags: [PyTorch]
categories: [Note]
---

This is a brief note on how to use [Tensorboard](https://github.com/tensorflow/tensorboard) in PyTorch.

<!--more-->

# Install

First we need to install tensorboard:

```
pip install tensorboard
```

# SummaryWriter

The main interface we use is [`SummaryWriter`](https://pytorch.org/docs/stable/tensorboard.html#torch.utils.tensorboard.writer.SummaryWriter).
It has many builtin functions, such as `add_scalar`, `add_image`,  `add_graph` (for torch models) etc.

For most use cases, we just need to use [`add_scalar()`](https://pytorch.org/docs/stable/tensorboard.html#torch.utils.tensorboard.writer.SummaryWriter.add_scalar).

```python
import numpy as np
import os
from torch.utils.tensorboard import SummaryWriter


def main():
    log_dir = "exp_log"
    if not os.path.exists(log_dir):
        os.makedirs(log_dir)

    writer = SummaryWriter(log_dir=log_dir)
    for i in range(50):
        writer.add_scalar("my curve", np.random.random(), i)

    # need to close the writer after training
    writer.close()
```

The first argument is the tag given to this value series.

## Group plots

We can also group the plot like this:

```python
for n_iter in range(100):
    writer.add_scalar('Loss/train', np.random.random(), n_iter)
    writer.add_scalar('Loss/test', np.random.random(), n_iter)
    writer.add_scalar('Accuracy/train', np.random.random(), n_iter)
    writer.add_scalar('Accuracy/test', np.random.random(), n_iter)
```

In the visualization, we will get two groups, one for `Loss` and one for `Accuracy`.
Each group has two plots, for `train` and `test` respectively.

## Compare stats in the same plot

Often we want to show/compare several curves on the same plot.
This can be achieved with [`add_scalars()`](https://pytorch.org/docs/stable/tensorboard.html#torch.utils.tensorboard.writer.SummaryWriter.add_scalars):


```python
for n_iter in range(100):
    writer.add_scalars('Loss', {'train': np.random.random(),
                                'test': np.random.random()}, n_iter)

    writer.add_scalars('Accuracy', {'train': np.random.random(),
                                    'test': np.random.random()}, n_iter)
```

In the above code, we have two groups, and each group has one plot showing both `train` and `test` stats.

## Change the axis scale?

Sometimes the scale for x and y axis may not be right.
The first thing you can do is to disable outlier removal, since it is enabled by default.

Or we can manually select a region using the mouse to only show that region.
After tensorboard [version 2.5](https://github.com/tensorflow/tensorboard/releases/tag/2.5.0), you can set axis range in the tensorboard web interface interactively,
thanks to the work of [this pr](https://github.com/tensorflow/tensorboard/pull/4711).

ref:

+ https://stackoverflow.com/questions/37144211/rescale-tensorboard-summaries-plot
+ https://github.com/tensorflow/tensorboard/issues/273

# Visualize the plot

To actually show the visualizations, we can run the following command:

```bash
tensorboard --logdir=exp_log
```

The argument `--logdir` should be followed one of valid tensorboard logs you have written during your experiment.
Then you can open the browser and check the plots.

# References

+ Tensorboard tutorial: https://pytorch.org/tutorials/intermediate/tensorboard_tutorial.html
+ PyTorch Tensorboard doc: https://pytorch.org/docs/stable/tensorboard.html
