---
title: "神经网络中误差反向传播(back propagation)算法的工作原理"
date: 2016-01-19 00:00:00 +0800
tags: [optimization, CNN, math]
categories: [academic]
markup: pandoc
---

神经网络，从大学时期就听说过，后面上课的时候老师也讲过，但是感觉从来没有真正掌握，总是似是而非，比较模糊，好像懂，其实并不懂。

为了搞懂神经网络的运行原理，有必要搞清楚神经网络最核心的算法，也就是[误差反向传播算法](https://en.wikipedia.org/wiki/Backpropagation)的工作原理，本文以最简单的全连接神经网络为例，介绍误差反向传播算法的原理。

<!--more-->
<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-24/16883425.jpg"
         title="简单的神经网络示意图">
</p>

在开始推导之前，需要先做一些准备工作，推导中所使用的神经网络如上图所示。一个神经网络由多个层 (layer) 构成，每一层有若干个节点 (node)，最左边是「输入层」，中间的层被称为「隐含层」，最右边是「输出层」；上一层节点与下一层节点之间，都有边相连，代表上一层某个节点为下一层某个节点贡献的权值。通常为了使得网络能够模拟复杂的关系，上一层节点的输出乘以权值矩阵得到下一层节点后，需要再经过「激活函数」对得到的各个节点的值进行非线性化处理。激活函数可以选用很多的形式，这里使用sigmoid 函数，表达式如下：

\begin{equation}\label{eq1}
f(x)=\frac{1}{1+\exp(-x)}
\end{equation}

公式 $\eqref{eq1}$ 之所以使用 sigmoid 函数，一个很重要的原因就是「mathematical convenience」，sigmoid 函数的导数很好计算，

\begin{equation}
f'(x)=f(x)(1-f(x))
\end{equation}\label{eq2}

接下来，对推导中使用的符号做一个详细的说明，使推导的过程清晰易懂。我们用 $L$ 代表网络的层数，用 $S_l$ 代表第 $l$ 层的节点的个数；用 $z^{(1)}$ 和 $a^{(1)}$ 表示第 $l$ 层经过激活函数前/后的节点向量 ($z^{(l)}_i$ 和 $a^{(l)}_i$ 代表经过激活函数前/后节点 $i$ 的值)，根据以上的表示，$a^{(1)}$ 代表网络的输入 $x$, $a^{(L)}$代表网络的输出 $y$, 也就是上图中的 $h_{W,b}(x)$；用 $W^{(l)}$ 表示第$l$ 层与第$l+1$ 层之间的权值形成的矩阵，$W^{(l)}_{ij}$ 代表 $l$ 层的节点 $j$ 与$l+1$ 层的节点 $i$ 之间的权重(注意这种表示方式),用 $b^{(l)}$ 代表 $l$ 层到$l+1$层之间的偏置向量。

有了上面的符号化表示，神经网络各层之间的关系，可以用简洁的向量矩阵形式来表达如下：

\begin{align}
z^{(l+1)} &= W^{(l)}a^{(l)}+b^{(l)} \label{eq3} \\
a^{(l+1)} &= f\left(z^{(l+1)}\right) \label{eq4}
\end{align}

根据以上的式子，我们就可以计算网络中每一层各个节点的值了，上述的过程称为「前向传播」(forward propagation) 过程，在深度学习库中通常都被叫做「forward」。

通常，网络刚创建好时，我们随机初始化每两层之间的权值矩阵以及偏置向量，但是这样得到的网络，输出与实际的值差距太大。使用神经网络的目的，当然是想要网络的输出与实际的值差距尽可能小，随机初始化网络，显然不能满足这个目的。但是如何调整各层之间的权值矩阵以及偏置呢，这并不是一个很简单的问题，下面要推导的反向传播(backward propagation) 算法就是解决这个问题的利器。

# 正式开始推导

通常来说，如果想要得到一个较好的网络，需要有一批已知的训练数据，假设我们现在总共有$m$个样本,即

$$\left\{(x^{(i)}, y^{(i)})\right\}, i= 1,2,\ldots,m$$

对于每一个样本来说，我们优化的目标为 ：

\begin{equation}\label{eq5}
J\left(W,b;x^{(i)}, y^{(i)}\right) = \min_{W,b} \frac{1}{2}\left\lVert h\left(x^{(i)}\right)- y^{(i)} \right\rVert^2
\end{equation}

公式 $\eqref{eq5}$ 中，为了简化，我们用 $h(x^{(i)})$ 表示 $h_{W,b}(x^{(i)}) $

对于所有样本来说,我们需要最小化的目标函数为：

\begin{equation}
\begin{aligned}
J(W, b) &= \frac{1}{m}\sum_{i=1}^{m}J\left(W,b;x^{(i)}, y^{(i)}\right) + \frac{\lambda}{2}\sum_{l=1}^{L-1}\left\lVert W^{(l)} \right\rVert_{F}^{2}\\
&= \frac{1}{m}\sum_{i=1}^{m}\frac{1}{2}\left\lVert h(x^{(i)}) - y^{(i)}\right\rVert^2 + \frac{\lambda}{2}\sum_{l=1}^{L-1}\left\lVert W^{(l)} \right\rVert_{F}^{2}
\end{aligned}\label{eq6}
\end{equation}

上述优化目标函数 $\eqref{eq6}$，并不简单是各个样本优化目标的和，而是由两项构成：第一项为误差项，第二项称为「正则项」(英文叫「regularization term」也称为weight decay term)，用来控制各层权值矩阵的元素大小，防止权值矩阵过大，网络出现过拟合，这和曲线拟合中对参数使用正则道理是一样的，只不过曲线拟合中，参数是向量，这里的参数是矩阵。我们使用 $F$ 范数的平方来约束权重矩阵元素的大小，正则项前面的系数 $\lambda$ (称为weight decay parameter) 用来控制正则项与误差项之间的权重。另外，一般来说，只对权值矩阵进行正则，不对偏置进行正则。

## 关于F范数的一点小知识

为了计算目标函数对权重矩阵的偏导，有必要对 $F$ 范数 (也称为 $F$-norm) 有所了解。假设矩阵 $A$ 是实数矩阵，大小为 $m\times n$,其 $F$ 范数用公式可以表示为：

\begin{equation}
\lVert A \rVert_F = \sqrt{\sum_{i=1}^{m}\sum_{j=1}^{n} (a_{ij})^2}
\end{equation}\label{eq7}

另外关于矩阵 $A$ 的 $F$ 范数对矩阵 $A$ 如何求导，有如下公式：

\begin{equation}\label{eq8}
\frac{\partial  \lVert A \rVert_F^2 }{\partial A}= 2A
\end{equation}

# 抽丝剥茧，不断深入

我们优化网络的目标是计算各层的权值矩阵以及偏置向量，使得优化目标函数取得最小值。根据[梯度下降算法](https://zh.wikipedia.org/wiki/%E6%A2%AF%E5%BA%A6%E4%B8%8B%E9%99%8D%E6%B3%95)，可以计算目标函数对各个参数的偏导，采用迭代方式来更新参数，最终得到最优的参数值。但是事实上，上述函数是非凸函数，梯度下降算法并不一定能够得到全局最优解(global optimum)，一般只能得到局部最优解(local optimum)。实际中得到的结果一般都是比较接近最优结果，在可以接受的范围之内，所以才使用梯度下降算法来优化神经网络。另外实际优化过程中，还有一些技巧，譬如加入 momentum 项，使得目标函数能够跳出local optimum 点，从而得到global optimum。在实际的深度学习训练中，一般都会使用momentum 来加快收敛的速度，这里仅讨论最基本情况，对增加 momentum 项的情况不予讨论。

如果已经求得目标函数对各个函数的偏导数，那么各个参数的更新公式如下：

\begin{align}
W_{ij}^{(l)} &= W_{ij}^{(l)} - \alpha \frac{\partial J(W,b)}{\partial W_{ij}^{(l)}} \label{eq9} \\
b_i^{(l)} &= b_i^{(l)} - \alpha \frac{\partial J(W, b)}{\partial b_i^{(l)}} \label{10}
\end{align}

以上的式子中，$\alpha$ 称为「学习率」(learning rate)，用来控制权重和偏置项的更新幅度，如果 $\alpha$ 太大，网络的参数收敛速度快，但是可能出现来回震荡的情况，甚至不能收敛；如果 $\alpha$ 太小，网络收敛速度太慢，训练时间长。需要说明，权重矩阵以及偏置向量的学习率可以不一样，根据需要分别设置，实际上，Caffe 就是这么做的，可以在 prototxt 里面指定每层的权重以及偏置的学习率，其他的深度学习框架也可以类似设置。

从上面的公式可以看出，现在的关键是，如何计算目标函数对权重矩阵以及偏置项各个元素的偏导，根据目标函数 $J(W, b)$ 的计算公式 $\eqref{eq6}$，可以得到目标函数$J(W, b)$ 对权重矩阵以及偏置项各元素的导数：

\begin{align}
\frac{\partial  J(W, b) }{\partial W_{ij}^{(l)}} &= \left[\frac{1}{m}\sum_{i=1}^{m}\frac{\partial J\left(W, b; x^{(i)}, y^{(i)}\right)}{\partial W_{ij}^{(l)}} \right] + \lambda W_{ij}^{(l)} \label{eq11}\\
\frac{\partial  J(W, b) }{\partial b_i^{(l)}} &= \frac{1}{m}\sum_{i=1}^{m}\frac{\partial J\left(W, b; x^{(i)}, y^{(i)}\right)}{\partial b_i^{(l)}} \label{eq12}
\end{align}

上面两个公式中,公式 $\eqref{eq11}$ 后半部分可以参考前面对于矩阵范数求导的公式$\eqref{eq8}$ 得到。

观察以上公式，接下来的问题就是，如何求取样本目标函数对于权重矩阵以及偏置向量的偏导，也就是如何求 $\frac{\partial}{\partial W_{ij}^{(l)}}J\left(W,b;x^{(i)},y^{(i)}\right)$ 以及 $\frac{\partial}{\partial b_{i}^{(l)}}J\left(W,b;x^{(i)},y^{(i)}\right)$？如果得到每个样本的目标函数对于各个参数的偏导，整个问题就解决了。

这就要用到我们前面所说的 back-propagation 的思想了，当我们把一个样本输入到网络，通过前向传播，得到最终的输出，最终输出与实际的值之间有误差，然后我们通过某种有组织有规律的方式把误差一层一层向前传播，得到误差相对于每一层参数的偏导，这是求解该问题的核心思想。

为了便于推导，再引入变量 $\delta_{i}^{(l)}=\frac{\partial}{\partial z_{i}^{(l)}}J\left(W,b;x^{(i)},y^{(i)}\right)$, 即最终的误差对每一层节点经过激活函数前的变量的偏导，用它来衡量某一层某个节点对最终误差的贡献量。

# 计算辅助变量的值

对于最后一层(第 $L$ 层)，我们可以很方便的计算 $\delta_i^{(L)}$，详细推导如下：

\begin{equation}
\begin{aligned}
\delta_{i}^{(L)}&=\frac{\partial}{\partial z_{i}^{(L)}}\left(\frac{1}{2}\left\lVert y - h(x) \right\rVert^2\right) \\
&= \frac{\partial}{\partial a_{i}^{(L)}}\left(\frac{1}{2}\left\lVert y - h(x) \right\rVert^2\right)\cdot \frac{\partial a_{i}^{(L)}}{\partial z_{i}^{(L)}}\\
&=\frac{1}{2}\left[ \frac{\partial}{\partial a_{i}^{(L)}}\sum_{j=1}^{S_L} \left(y_j - a_j^{(L)}\right)^2 \right] \cdot \frac{\partial a_{i}^{(L)}}{\partial z_{i}^{(L)}}\\
&=-\left(y_{i}-a_{i}^{(L)}\right)f'\left(z_{i}^{(L)}\right)
\end{aligned}\label{eq13}
\end{equation}

上述公式 $\eqref{eq13}$ 中，$f'\left(z_{i}^{(L)}\right)=a^{(L)}_{i}\left(1-a^{(L)}_{i}\right)$ ，对于其他层也是如此计算，不再赘述。对于其他层($l=L-1,L-2,\cdots,2$)的辅助变量，计算就不那么容易了，因为输出误差并不直接和这些层的节点相关，所以我们需要构造关系，利用微积分里面的[链式法则](https://en.wikipedia.org/wiki/Chain_rule) (chain rule),具体计算过程如下：

\begin{equation}
\begin{aligned}
\delta_{i}^{(l)}&=\frac{\partial}{\partial z_{i}^{(l)}}J(W,b;x,y) \\
&=\sum_{j=1}^{s_{l+1}}\frac{\partial J}{\partial z_{j}^{(l+1)}}\cdot \frac{\partial z_{j}^{(l+1)}}{\partial z_{i}^{(l)}}\\
&=\sum_{j=1}^{s_{l+1}}\delta_{j}^{(l+1)}\cdot W_{ji}^{(l)}f'\left(z_{i}^{(l)}\right)\\
&=\left(\sum_{j=1}^{s_{l+1}} W_{ji}^{(l)}\delta_{j}^{(l+1)}\right)\cdot f'\left(z_{i}^{(l)}\right)
\end{aligned}\label{eq14}
\end{equation}

为了便于书写，上面公式$\eqref{eq14}$中，$J(W,b;x,y)=J$。求误差对 $l$ 层某个节点的偏导，无法直接求解，因为误差只和最后一层的节点有直接关系，但是如果我们已经知道了误差相对于下一层 (也就是 $l+1$ 层) 节点的偏导，而下一层节点和本层 ($l$ 层)直接相关，那么整个链条就可以打通了。关于如何由第一行得到第二行，我起初并没有正确得到，后来结合网上给的参考结果，逐渐想通如何计算。微积分中有针对多个中间变量的链式法则，其思想为：如果目标变量 (dependent varialbe)是三个中间变量(intermediate variable) $P,Q,R$ 的函数，而这三个中间变量又是自变量 (independent variable) $x$ 的函数，那么很容易证明下面的式子成立，

\begin{equation}\label{eq15}
\frac{\partial \, obj}{\partial x}=\frac{\partial \, obj}{\partial P}\cdot \frac{\partial P}{\partial x}+\frac{\partial \, obj}{\partial Q}\cdot \frac{\partial Q}{\partial x}+\frac{\partial \, obj}{\partial R}\cdot \frac{\partial R}{\partial x}
\end{equation}

上述公式 $\eqref{eq14}$ 中第二行的求和符号就是这么来的，起初推导时少了求和符号，只求了误差相对于下一层节点 $i$ 的偏导，没有意识到下一层的每个节点其实与上一层的每个节点都有关系, 利用链式法则，我们就可以很容易求得误差相对于本层的偏导，这就是误差反传的思想。

根据我们前面的定义，上面的公式 $\eqref{eq14}$，第二行求和符号里面的第一项$\frac{\partial J}{\partial z_{j}^{(l+1)}}$，等于 $\delta_{j}^{(l+1)}$。第二项如何显式表达出来呢？结合公式 $\eqref{eq3}$ 和 $\eqref{eq4}$，具体如下：

\begin{equation}
\begin{aligned}
z_{j}^{(l+1)}&= \left [\sum_{i=1}^{s_l} W_{ji}^{(l)}\cdot a_{i}^{(l)} \right]+b_{j}^{(l)} \\
&=\left [\sum_{i=1}^{s_l} W_{ji}^{(l)}\cdot f\left(z_i^{(l)}\right)\right]+b_{j}^{(l)}
\end{aligned}\label{eq16}
\end{equation}

有了公式 $\eqref{eq16}$，那么公式 $\eqref{eq14}$ 第二行第二项偏导就很容易得到了：

\begin{equation}
\frac{\partial z_{j}^{(l+1)}}{\partial z_{i}^{(l)}}=W_{ji}^{(l)}f'(z_{i}^{(l)})
\end{equation}

这就是公式 $\eqref{eq14}$ 第三行第二项的来历。

# 计算误差相对于矩阵元素和偏置向量元素的偏导

有了以上的铺垫，我们现在可以计算误差相对于矩阵元素以及偏置向量元素的偏导了。

\begin{equation}
\begin{aligned}
\frac{\partial J}{\partial W_{ij}^{(l)}}&=\frac{\partial J}{\partial z_{i}^{(l+1)}}\cdot\frac{\partial z_{i}^{(l+1)}}{\partial W_{ij}^{(l)}} \\
&= \delta_{i}^{(l+1)}\cdot a_{j}^{(l)}
\end{aligned}
\end{equation}

\begin{equation}
\begin{aligned}
\frac{\partial J}{\partial b_{i}^{(l)}}&=\frac{\partial J}{\partial z_{i}^{(l+1)}}\cdot\frac{\partial z_{i}^{(l+1)}}{\partial b_{i}^{(l)}}\\
&= \delta_{i}^{(l+1)}\cdot 1 \\
&= \delta_{i}^{(l+1)}
\end{aligned}
\end{equation}

# 向量化表示

对于输出层：

\begin{equation}
\delta^{(L)}=-\left(y-a^{(L)}\right)\cdot f'\left(z^{(L)}\right)
\end{equation}

对于其他层 ($l=L-1,L-2,\cdots,2$):

\begin{equation}
\delta^{(l)}=\left[\left(W^{(l)}\right)^{T}\delta^{(l+1)}\right]\cdot f'\left(z^{(l)}\right)
\end{equation}

权重以及偏置更新公式：

\begin{align}
\frac{\partial J}{\partial W^{(l)}} &= \delta^{(l+1)}\left(a^{(l)}\right)^{T}\\
\frac{\partial J}{\partial b^{(l)}} &= \delta^{(l+1)}
\end{align}

# 把所有公式整合在一起

现在我们可以把所有的公式结合在一起，得出最终的参数更新公式了。

1. 初始化，对于所有层 ($l=1,2,\cdots,L-1$)，令 $\Delta W^{(l)}=0$, $\Delta b^{(l)}=0$,前一项是一个矩阵，后一项是一个向量，分别代表对权重矩阵以及偏置向量的更新量。

2. 对于一个batch的所有训练样本 (for i=1 to m)

    + 使用误差反传计算 $\nabla_{W^{(l)}}J\left(W,b;x^{(i)},y^{(i)}\right)$ 和 $\nabla_{b^{(l)}}J\left(W,b;x^{(i)},y^{(i)}\right)$
    + $\Delta W^{(l)}:= \Delta W^{(l)}+\nabla_{W^{(l)}}J\left(W,b;x^{(i)},y^{(i)}\right)$
    + $\Delta b^{(l)}:= \Delta b^{(l)}+\nabla_{b^{(l)}}J\left(W,b;x^{(i)},y^{(i)}\right)$

3. 更新参数

\begin{align}
W^{(l)} &= W^{(l)}-\alpha\left[\left(\frac{1}{m}\Delta W^{(l)}\right) + \lambda W^{(l)} \right] \\
b^{(l)} &= b^{(l)}-\alpha\left[\frac{1}{m}\Delta b^{(l)}\right]
\end{align}

至此，误差反传以及参数更新的全部内容完成！

# 参考资料

+ <http://ufldl.stanford.edu/wiki/index.php/Backpropagation_Algorithm>
+ <https://en.wikipedia.org/wiki/Backpropagation>
+ <http://neuralnetworksanddeeplearning.com/chap2.html>
