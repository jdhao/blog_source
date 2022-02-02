---
title: "向量搜索引擎/工具"
date: 2021-07-24T21:28:11+08:00
draft: false
tags: [search]
categories: [Note]
---

无论是推荐还是检索任务，都离不开向量检索引擎，当向量数量巨大(上千万或者上亿规模)的时候，使用精确搜索(exhaustive search)的时间成本对于用户来说无法接受，这时我们必须用到近似最邻近搜索（approximate nearest neighbor, 简称 ANN)。本文总结了一些常见的实现了 ANN 搜索的向量搜索引擎/工具。

<!--more-->

关于主流向量搜索引擎/库的评测，可以参考 [ANN benchmark](https://github.com/erikbern/ann-benchmarks)。

# 向量搜索库和引擎的区别？

简单理解，向量搜索库更轻量化，可能不支持分布式，仅仅是作为 Library 存在，向量搜索引擎实现了一整套特征存储，更新，删除，分布式等特性，而且向量搜索引擎底层可能会用到这些向量搜索库，譬如 vearch 会用到 FAISS，Milvus 也会用到 FAISS, nmslib 等。

# 向量搜索库

## FLANN

[FLANN](https://github.com/flann-lib/flann) 是 SIFT 发明者 Lowe 开发的近似搜索库。

## Annoy

[Annoy](https://github.com/spotify/annoy) 是 spotify 开源的一款近似搜索工具，不支持分布式搜索。

## Faiss

[Faiss](https://github.com/facebookresearch/faiss) 是 Facebook AI Research 开源的一款向量搜索库[^2]，实现了多种向量索引算法。不过 [Faiss 不支持分布式搜索](https://github.com/facebookresearch/faiss/issues/840)，如果向量数据量很大，Faiss 就不合适了。

Faiss 原始论文「Billion-scale similarity search with GPUs」见 [这里](https://arxiv.org/abs/1702.08734)。

## ScaNN

[ScaNN](https://github.com/google-research/google-research/tree/master/scann) 是谷歌在 2020 年发布的一款向量检索工具[^1]，基于谷歌在 ICML 2020 上发表的一篇文章 [Accelerating Large-Scale Inference with Anisotropic Vector Quantization](https://arxiv.org/abs/1908.10396)。

快手推荐中台在使用 ScaNN 作为 ANN 搜索工具[^3]。

## NMSLIB

[nmslib](https://github.com/nmslib/nmslib) 是一款近似搜索工具，实现了一些近似搜索算法，如 HNSW。

## hora

[Hora](https://github.com/hora-search/hora) 是一款用 Rust 实现的 ANN 搜索库，实现了多种 index 和多种距离度量。

# 向量搜索引擎

## Vearch

[Vearch](https://github.com/vearch/vearch) 是京东开源的向量搜索框架，基于 Faiss 开发，我们目前用的向量搜索引擎就是 vearch，支持分布式搜索。

优点：Vearch 支持的距离有两种，L2 距离和内积（如果是归一化的向量，可以认为是余弦相似度），也支持根据标量字段进行过滤，总的来说功能还是可以的。

缺点：感觉 vearch 不是很稳定，文档写的也很烂（非常简略），另外 vearch 虽然在维护中，开发不是很活跃。

## Milvus

[Milvus](https://github.com/milvus-io/milvus) 是近两年新开源的一款全新的向量搜索引擎，开发很活跃，感觉未来很有希望。目前 Milvus 2.0 还在 rc 阶段，支持了标量字段过滤功能，功能还不完善，例如现在还[不支持删除特征操作](https://github.com/milvus-io/milvus/blob/master/milvus20vs1x.md)，总体功能不如 vearch 丰富。

Milvus 搜索策略，不在 index 的向量 brute force，在 index 的向量是近似搜索，最后两者融合，参考[这里](https://news.ycombinator.com/item?id=22017976)讨论。

Milvus 系统设计论文，被 ACM SIGMOD 21 接收，见[这里](https://zilliz.com/whitepapers/milvus-a-purpose-built-vector-data-management-system).

## SPTAG

[SPTAG](https://github.com/microsoft/SPTAG) (Space Partion Tree And Graph) 是微软发布的一款分布式向量搜索工具，知名度不是很高，没怎么听说有哪个公司在用？

## Vald

[Vald](https://github.com/vdaas/vald) 是 Go 语言实现的分布式向量检索系统，主要由日本开发人员开发，雅虎日本在使用，其他地方未看到使用案例。

## Proxima

Proxima 是阿里巴巴开发的向量搜索引擎，介绍可以参考[这篇文章](https://www.sohu.com/a/454661346_612370)，不过它没有开源，从介绍文章上来看很牛逼，性能超过 FAISS，具体如何不太清楚。

# 参考资料

+ https://github.com/currentsapi/awesome-vector-search
+ Milvus 在 hacker news 上帖子：https://news.ycombinator.com/item?id=22012300
+ Proxima
    + 深度揭秘达摩院向量检索引擎Proxima：https://www.sohu.com/a/454661346_612370
    + 大数据搜索技术下一站：浅谈向量检索的现状、挑战和未来：https://www.infoq.cn/article/j3vvye0uhlfdpnc8uyrs
    + https://www.slidestalk.com/u45737/7Proxima72523

[^1]: https://ai.googleblog.com/2020/07/announcing-scann-efficient-vector.html
[^2]: https://engineering.fb.com/2017/03/29/data-infrastructure/faiss-a-library-for-efficient-similarity-search/
[^3]: 消息来源：https://www.pojan.cn/kuaishou/6118.html
