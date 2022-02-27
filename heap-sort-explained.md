---
title: "堆排序是如何工作的？"
date: 2017-10-06 16:25:03 +0800
tags: [algorithms, sorting]
categories: [Programming]
markup: pandoc
---

本文将介绍堆 (heap)的概念，进一步介绍如何把普通数组变成最大堆，并在此基础上进行堆排序。

<!--more-->

# 基础概念

要谈堆排序，必须先说清楚“堆”这种数据结构，堆的定义如下(来自维基百科)：

> In computer science, a heap is a specialized tree-based data structure that
> satisfies the heap property: if P is a parent node of C, then the key (the
> value) of node P is greater than the key of node C. A heap can be classified
> further as either a "max heap" or a "min heap". In a max heap, the keys of
> parent nodes are always greater than or equal to those of the children and the
> highest key is in the root node. In a min heap, the keys of parent nodes are
> less than or equal to those of the children and the lowest key is in the root
> node.

在本文中，我们讨论的均是最大堆（max heap）。堆是一种完全二叉树(complete binary tree)，所谓完全二叉树，就是满二叉树 (full binary tree)去掉最后一层右边的一些节点构成的二叉树。关于二者的定义，可以参考[这里](https://stackoverflow.com/a/32064101/6064933)。下面是一张形象的表示图，

<img src="https://blog-resource-1257868508.file.myqcloud.com/complete_binary_tree.png">

我们可以按照从上到下，从左到右的顺序给堆的节点编号，根节点编号为 $0$，如上图中右图所示（注意上图并不是一个最大堆或者最小堆，仅仅是示意可以这样来给堆的节点编号。这样的话，堆可以用一个数组 $A$ 来表示，$A[0]$ 表示堆的根节点的值。 由于堆是完全二叉树，计算编号为 $m$ 的节点的父节点以及左右子节点(如果左右子节点存在的话)非常容易，计算公式由以下式子给出，

父节点：$\lfloor \frac{m-1}{2}\rfloor$
左子节点：$2m + 1$
右子节点：$2m + 2$

证明如下，假设根节点所在的层为第 $0$ 层，那么第 $i$ 层节点个数为 $2^i$ 个, 第 $i$ 层各个节点编号为 $2^i - 1$(第 1 个节点)，$2^i + k - 2$（第 k 个节点），$2^{i+1} - 2$（第 $2^i$ 个节点）。对于第 $i$ 层第 $k$ 个节点(编号是 $2^i + k - 2$)，其在下层的左子节点编号计算公式为

$$\begin{equation}\begin{aligned}
\text{idLeftChild} &= \left [ 2^i + k - 2 - (2^i - 1)\right ] + 2^{i+1} - 1\\
      &= 2k - 2 + 2^{i+1} - 1 \\
      &= 2^{i+1} + 2k - 4 +1 \\
      &= \left (2^i + k - 2 \right )*2 + 1
\end{aligned}\end{equation}$$

从以上公式可以归纳出，编号为 $m$ 的节点，其左子节点编号为 $2m+1$，右子节点编号为 $2m+2$，其父节点编号为 $\lfloor \frac{m-1}{2}\rfloor$。

# 任意普通数组的“堆化”

因为完全二叉树可以用数组表示，反过来任意的数组也对应一个完全二叉树，只是这个完全二叉树不一定是最大堆。 给定一个数组，如何把它变为一个堆呢？观察一个最大堆，我们会发现，除了叶子节点，其余每个节点都满足最大堆的特点，因此要把一个数组对应的完全二叉树变为最大堆，只需要自底向上，逐层把各个非叶子节点逐一最大堆化即可，同时要保证最大堆的特性在最大堆化过程中不被破坏。

对于大小为 $n$ 的数组 $A$，很容易看出从节点 $0$ 到节点 $\lfloor \frac{n}{2} - 1\rfloor$ 含有叶子节点，其余节点均为叶子节点，所以最大堆化的过程只需要对这些非叶子节点进行。下面是最大堆化的 C++ 实现，

```cpp
void max_heapify(vector<int>& arr, const int N, int i){
    int largest = i;
    int l = 2*i + 1;
    int r = 2*i + 2;
    if (l < N && arr[l] > arr[largest])
        largest = l;
    if (r < N && arr[r] > arr[largest])
        largest = r;

    // if largest val is not in parent node, we need to
    // switch val of parent node and largest val
    if (largest != i){
        swap(arr[i], arr[largest]);
        max_heapify(arr, N, largest);
    }
}

void make_heap(vector<int>& arr, const int N){
    for (int i = N/2 -1; i >= 0; --i){
        max_heapify(arr, N, i);
    }
}
```

# 堆排序的思想与实现

对于的给定的数组，堆排序算法工作原理如下

1. 把当前的无序数组变为最大堆（arr[0] 为数组最大元素）
2. 对于 i 从 n-1 到 1，
    1. 交换 arr[0] 与当前数组的最后一个元素的位置 arr[i-1]，然后把堆的大小减 1
    2. 把剩余的二叉树的根节点重新变为最大堆化（因为该二叉树只有根节点不满足最大堆的条件

把以上算法写成代码如下

```cpp
void heap_sort(vector<int>& arr){
    const int N = static_cast<int>(arr.size());
    make_heap(arr, N);

    for (int i = N-1; i >= 1; --i){
        swap(arr[0], arr[i]);
        max_heapify(arr, i, 0);
    }
}
```

下面是一个完整的可以运行的例子，

```cpp
#include <bits/stdc++.h>
using namespace std;
void printArray(const vector<int>& arr){
    for(auto& num: arr){
        cout << num << " ";
    }
    cout << endl;
}

int main(){
    const int N = 10;
    const int rangeBegin = 0;
    const int rangeEnd = 1000;

    random_device rd;
    mt19937 gen(rd());
    uniform_int_distribution<int> distribution(rangeBegin, rangeEnd);

    vector<int> arr(N, 0);
    for (int i = 0; i != N; ++i){
        arr[i] = distribution(gen);
    }

    cout << "Original array: ";
    printArray(arr);

    heap_sort(arr);
    cout << "After heap sort: ";
    printArray(arr);


    return 0;
}
```

示例运行结果如下：

<img src="https://blog-resource-1257868508.file.myqcloud.com/heap_sort_runing_example.png">

# 时间复杂度，空间复杂度及稳定性

构建最大堆的时间复杂度为 $O(n)$ (并不是 $O(\log(n))$，详细论证过程见[这里](https://stackoverflow.com/a/18295327/6064933))，heap sort 时间复杂度为$O(n\log(n))$，因此总的时间复杂度为 $O(n\log(n))$，由于堆排序只使用了常量空间，因此该算法空间复杂度是 $O(1)$。

前面的博客也提到过，[排序算法的稳定性在某些情况下很重要](https://jdhao.github.io/2017/09/30/sorting-algorithms-stability/). 本文中的堆排序属于不稳定的算法，举例来说明，假设输入的数组是 {2a, 2b, 1} (a, b 用来区分相同值的先后顺序)，这个数组已经是最大堆了，使用 heap sort 算法，得到的排序后的结果应该是 {1, 2b, 2a}，相同数值的先后顺序被破坏，这就说明了堆排序是不稳定的算法。

---

# Reference

+ [一个很详细的堆排序讲解](https://www.programiz.com/dsa/heap-sort).
+ [Difference between full binary tree and complete binary tree](https://stackoverflow.com/q/12359660/6064933).
+ [为什么堆排序是不稳定的排序算法](https://stackoverflow.com/q/19336881/6064933).
