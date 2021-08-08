---
title: When Does the Stability of Sorting Algorithms Matter?
date: 2017-09-30 15:34:55 +0800
tags: [algorithms, sorting]
categories: [Programming]
---

In this post, I will write about the stability of sorting algorithms and why it
is important.

<!--more-->

When we read articles about sorting algorithms, a term that often appears is
the "[stability](https://en.wikipedia.org/wiki/Sorting_algorithm#Stability)" of
an algorithm. The definition of stability is pretty simple: Given an unsorted
list and a sorting algorithm. If two equal elements in the original list remain
their relative order in the sorted list, then the sorting algorithm is stable,
otherwise it is an unstable algorithm.

Now that we know the definition and there tons of article telling you which
sorting algorithm is stable and which is not, we may wonder what is the point
of identifying if an algorithm is stable or not. In fact, if you only have a
list of single items to sort, the stability of the sorting algorithm is not
important. However, if you have a list where each element is some kind of
record which has several keys, you way want to sort the record based on several
keys. That is where stability comes into play.

For example, we have a list of some students who belong to different labs,[^1]

```
Dave  A
Alice B
Ken   A
Eric  B
Carol A
```

We want to sort the records first using the student names as the key, the
result is:

```
Alice B
Carol A
Dave A
Eric B
Ken A
```

Then we want to sort the above result further based on their labs, we may get
the result of 

```
Dave A
Ken A
Carol A
Alice B
Eric B
```

or 

```
Carol A
Dave A
Ken A
Alice B
Eric B
```

If the use a stable algorithm, it will ensure that the order of previous
sorting will be preserved if two keys are the same. So the names corresponding
to the same labs are in sorted order. The result will be

```
Carol A
Dave A
Ken A
Alice B
Eric B
```

In summary, the stability of sorting algorithms is important only when you want
to chain multiple sort according to different keys in the record. If your
record only has one key, the stability issue is irrelevant.

# References

+ <https://stackoverflow.com/questions/1517793/what-is-stability-in-sorting-algorithms-and-why-is-it-important>
+ <https://stackoverflow.com/questions/808617/what-is-the-benefit-for-a-sort-algorithm-to-be-stable/808668#808668>
+ <http://www.drdobbs.com/cpp/why-stable-sorting-is-important/231001864>

[^1]: The example is from [this page](http://cs.smu.ca/~porter/csc/common_341_342/notes/sorts_stable.html).
