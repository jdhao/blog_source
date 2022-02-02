---
title: "Distributed Training in PyTorch with Horovod"
date: 2019-11-01 22:26:53+0800
tags: [PyTorch]
categories: [machine-learning]
---

[Horovod](https://github.com/horovod/horovod) is the distributed training
framework developed by Uber. It support training distributed programs with
little modification for both TensorFlow, PyTorch, MXNet and keras.

<!--more-->

# Basic concepts of MPI

For distributed training, horovod relies on MPI or Gloo, both of which are
libraries developed for parallel computing. The underlying concepts may be
similar.

Communicator: this is a common world for a group of processes. Each process
inside the communicator has its rank, which is a unique id used by MPI to
identify a particular process.

Point-to-point communication: it means that a process interacts with another
process directly in the communicator.

Collective operation: A MPI collective operation involves all the processes in
a communicator. These operations include broadcast, gather, scatter, reduce,
etc.

## The meaning of some collective operations

+ Broadcast means to copy the same data from the root process to the other processes.
+ Gather means to take data from other processes and put them in the root process.
+ Scatter means to slice the dataset in root process to several parts and distribute one part to each process.
+ Reduce means to perform some sort of action (sum, max, min, etc.) on the data from all processes and put the result in the root process.
+ Allreduce is based on reduce operation and it copies the result to other processes.

## References

+ [Introduction to MPI programming.](https://www.uio.no/studier/emner/matnat/ifi/INF3380/v11/undervisningsmateriale/inf3380-week06.pdf)
+ [Parallel computing with MPI.](https://indico.desy.de/indico/event/12535/session/2/contribution/30/material/13/0.pdf)
+ [Wiki page on MPI concepts.](https://en.wikipedia.org/wiki/Message_Passing_Interface#Concepts)

# What are world size, rank, local rank?

First, the meaning of world in distributed scenario:

> By default, collectives are executed on all the processes, also known as world.

In PyTorch, distributed training using `torch.dist.DistributedParallel`, the
number of spawned processed equals to the number of GPUs you want to use. Rank
is the unique id given to each process, and local rank is the local id for GPUs
in the same node.

For example, if you want to use 2 nodes and 4 GPUs per node, then 2*4 =8
processes will be spawned. World size is 8. In node 1, the process rank is 0,
1, 2, 3, and in node 2, the process rank is 4, 5, 6, 7. In both the two nodes,
local rank would be 0, 1, 2, 3.

In horovod, the concept is similar. Usually, each GPU corresponds to one
process. Size is equal to the number of processes.

# How does batch size and multi-GPU training work together?

In PyTorch, for single node, multi-GPU training (i.e., using
[`torch.nn.DataParallel`](https://pytorch.org/docs/stable/nn.html#torch.nn.DataParallel)),
the data batch is split in the first dimension, which means that you should
multiply your original batch size (for single node single GPU training) by the
number of GPUs you want to use if you want to the original batch size for one
GPU.

For multi-node, multi-GPU training using horovod, the situation is different.
In this case, we first need to use a
[`DistributedSampler()`](https://pytorch.org/docs/stable/data.html#torch.utils.data.distributed.DistributedSampler)
like the following command:

```
train_sampler = torch.utils.data.distributed.DistributedSampler(
    train_dataset, num_replicas=hvd.size(), rank=hvd.rank())
```

In the above statement, the parameter `num_replicas` is the world size, and
parameter `rank` is the global rank (in contrast to the local rank) of current
process. But how does a DistributedSampler work?

You can find the source code of `DistributedSampler`
[here](https://pytorch.org/docs/stable/_modules/torch/utils/data/distributed.html#DistributedSampler).
According to the code, roughly speaking, it splits the dataset into
`num_replicas` parts, and according to its rank, each process get a part of the
original indexes of the dataset:

```
indices  =  indices[self.rank:self.total_size:self.num_replicas]
```

In the end, this sampler gets `len(dataset)/num_replicas` samples.

In the dataloader part, we need to use this distributed sampler instead of the
plain simple sampler:

```
kwargs = {'num_workers': 4, 'pin_memory': True} if args.cuda else {}
train_loader = torch.utils.data.DataLoader(
    train_dataset, batch_size=batch_size,
    sampler=train_sampler, **kwargs)
```

The size of this `train_loader`, i.e., the number of iterations for a single
process, is `len(dataset)/(num_replicas*batch_size)`. In a one node one GPU
scenario, the dataloader size is usually `len(dataset)/batch_size`. So in a
distributed scenario, the number of iterations for a single process is the
number of the iterations in one node one GPU case divided by `num_replica`. By
iterating over their respective data part, all the processes have finished one
epoch in the whole dataset.

Let's take a concrete example to illustrate the idea. Suppose the dataset size
is 1024 and batch size is 32. In one node one GPU case, the number of
iterations in one epoch is 1024/32=32.

If we instead use two nodes with 4 GPUs for each node. In total, 2*4=8
processes are started for distributed training. In this case, each process get
1024/8=128 samples in the dataset. The number of iterations for each process in
an epoch is 128/32=4.

Now you can see why distributed training is faster because each GPU only needs
to do a fraction of the jobs when only one GPU is used. Theoretically, the
acceleration you will get is N, where N is the number of GPUs used in the
distributed training.  In reality, you won't get that much acceleration for
your program due to communication costs and other factors.

## References

+ https://discuss.pytorch.org/t/a-question-concerning-batchsize-and-multiple-gpus-in-pytorch/33767/2
+ https://stackoverflow.com/questions/54216920/how-to-use-multiple-gpus-in-pytorch
+ [Horovod distributed training for imagenet classification.](https://github.com/horovod/horovod/blob/master/examples/pytorch_imagenet_resnet50.py)
