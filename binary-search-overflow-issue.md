---
title: "从二分搜索实现中的一个 bug 说起"
date: 2017-08-27 22:01:50 +0800
tags: [C++, algorithms, search]
categories: [Programming]
---

# 一道二分搜索问题

下午做了[一道 Leetcode 的编程题目](https://leetcode.com/problems/first-bad-version/description/)，牵扯出了二分搜索实现中需要特别注意的一个问题，题目如下

<!--more-->

>You are a product manager and currently leading a team to develop a new
product. Unfortunately, the latest version of your product fails the quality
check. Since each version is developed based on the previous version, all the
versions after a bad version are also bad.
>
>Suppose you have n versions [1, 2, ..., n] and you want to find out the first
bad one, which causes all the following ones to be bad.
>
>You are given an API bool isBadVersion(version) which will return whether
version is bad. Implement a function to find the first bad version. You should
minimize the number of calls to the API.

这道题可以使用二分搜索来解决，我写出了自认为正确的算法，运行的时候却总是提示 “Time Limit Exceeded”，最初的完整代码如下：

```cpp
int firstBadVersion(int n) {
  int head = 1, tail = n;
  while (head != tail) {
    int mid = (head + tail) / 2;
    if (isBadVersion(mid)) {
      tail = mid;
    } else {
      head = mid + 1;
    }
  }
  return head;
}
```

看了一下这道题目的讨论，有人也遇到了同样的问题，解决办法出奇简单。问题的关键就在下面这行看似正确实则错误的代码：

```cpp
int mid = (head + tail)/2;
```

改正这个错误也很简单，只需要将上述的写法换成另外一种「形式上等价」的写法：

```cpp
int mid = head + (tail - head)/2;
```

之所以说是「形式上等价」是因为，仅从数学推导上来说，上面两种写法结果是一样的，但是计算机科学并不是仅仅是理论，实际中，每种基本数据类型所能表达的数字都是有范围的，如果超过了范围，我们必须考虑整形溢出 (overflow) 问题。假设 `head` 与 `tail` 都不超过 `int` 所能表示的最大正整数（用 `INT_MAX` 表示），我们并不能保证 `head + tail` 不超过 `INT_MAX`，如果两者之和大于 `int` 类型所能表达的最大的正整数时(该数为 $2^{31}-1$)，就会发生溢出；采用第二种写法，由于每一步的计算都没有溢出的可能，最终的结果也不会溢出，因此是更安全的写法。

根据[Wiki](https://en.wikipedia.org/wiki/Binary_search_algorithm#Implementation_issues)上的说法，市面上流传的很多二分搜索的实现都存在溢出问题。就连大名鼎鼎的《Programming Pearls》这本书在 1986 年版本上提供的也是第一种写法，直到二十多年后才改为第二种写法；Java 语言中的二分搜索实现起初也存在这个溢出 bug，这个 bug 存在了长达九年才被更正。在计算机发展早期,可能谁也没有预料到，未来的数据量会如此庞大，以至于数组的 index 相加也会造成溢出。Google Research Blog 也曾发布过[一篇关于二分搜索实现的博文](https://research.googleblog.com/2006/06/extra-extra-read-all-about-it-nearly.html)。

# 当溢出发生，到底会发生什么？

你可能会好奇，如果发生溢出，到底会有什么后果呢？例如，如果一个 `int` 类型的数 `num` 等于 `INT_MAX`，那么 `num` 再加上一个正数，会发生什么？C++ 标准对此有明确规定，

>If during the evaluation of an expression, the result is not mathematically
defined or not in the range of representable values for its type, the behavior
is undefined, unless such an expression is a constant expression, in which case
the program is ill-formed. [Note: most existing implementations of C++ ignore
integer overflows].

也就是说，`num` 再加上一个正数，这是 "Undefined behaviour"，任何结果都有可能出现。

上述结果是对于有符号类型 (signed type) 来说的，对于无符号类型 (unsigned type)，C++ 标准有如下规定：

> This implies that unsigned arithmetic does not overflow because a result that
> cannot be represented by the resulting unsigned integer type is reduced
> modulo the number that is one greater than the largest value that can be
> represented by the resulting unsigned integer type.

翻译过来就是：无符号类型不会发生溢出。如果一个无符号类型的数加上一个正数产生的结果大于该无符号类型所能表示的最大数，那么最终结果就是原结果与最大数加1取余的结果。

举个例子，假设 `num` 是 `unsigned int` 所能表达的最大的数，那么 `num + 1 =  0`， `num + 2 = 1`，以此类推。一个示例程序如下：

```cpp
#include <iostream>
#include <limits>
using namespace std;

int main(){
    unsigned int num = std::numeric_limits<unsigned int>::max();

    for (int i = 1; i <= 3; i++){
        unsigned result = num + i;
        cout << result << (i==3?'\n':' ');
    }

    return 0;
}
```

该程序运行输出结果为：

```bash
0 1 2
```

(全文完)

# 参考

+ [当溢出产生，到底会发生什么？Stackoverflow 上的一个讨论。](https://stackoverflow.com/q/12275248/6064933)
