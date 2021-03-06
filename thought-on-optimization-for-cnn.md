---
title: 关于神经网络优化的一些思考
date: 2016-11-25
tags: [optimization]
categories: [academic]
---

优化的方法有很多种，在深度学习中，占有绝对主导地位的还是 stochastic gradient
optimization (简称 SGD) 以及它的一些变种，如 SGD with momentum，Adam 等。 SGD
是一种基于一阶梯度信息的优化方法，仅从优化的速度上来讲，效率不是最高的，一些利
用二阶信息的优化方法，理论上优化速度更快，但是，SGD 反而是在深度学习的优化中使
用的最多的优化方法，为什么其他类型的优化方法在深度学习中不经常使用呢？它们相比
SGD 有什么缺点？或者说 SGD 有什么优点呢？ 这篇文章试图对这个问题给出自己的思考
与总结。

<!--more-->

本文基于大量参考资料，对其中的要点进行提炼，主要介绍三种常见的优化方法：基于启
发式搜索的方法，基于一阶梯度信息的方法以及基于二阶梯度信息的方法。同时对实际使
用中一些论文中常用的术语以及它们的区别进行了说明。由于文章有一部分公式，所以我
采用了 LaTeX 书写本文，生成的 PDF 请[点击这里下载](/files/optimization-for-cnn.pdf)。
