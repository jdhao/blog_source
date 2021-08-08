---
title: "What Is the Correct Way of Loop Through C++ Vector Using Index?"
date: 2017-10-07 23:38:41 +0800
tags: [C++]
categories: [Programming]
---

It might seem naive to ask how to loop through a C++ vector using index.
However, it is not: even experienced programmer may write buggy code to loop
through a vector.

<!--more-->

I have long been familiar with code snippet below:

```cpp
vector<int> arr(100, 0);
for (int i = 0; i != arr.size(); ++i){
    // do something with arr[i]
}
```

Each time I write code like this, the compiler gives me warnings:

>comparison between signed and unsigned integer expressions [-Wsign-compare]

The reason is simple: `arr.size()` has type `vector<int>::size_type`, which is
unsigned. We are comparing signed types (`int`) with unsigned types
(`vector<int>::size_type`), hence the warning. If you insist on using int as
index for vector types, [you are for some buggy code](https://stackoverflow.com/a/6578066/6064933).
The better way is to use unsigned type
as index for vector, but we need to be careful.

# Choosing the index type: `size_t` or `size_type`?

Theoretically, we should declare the type of index `i` to be `vector<int>::size_type`.
But it is a lot of code to type. So I prefer to use `size_t` as the
type of index, which is also an unsigned type. Most of the time, you can safely
use `size_t` as the index. If you are interested, you can check the difference
between `size_type` and `size_t` [here](https://stackoverflow.com/q/4849678/6064933).

# How to loop forward

Looping forward through a vector using index is straightforward. See the sample
code below:

```cpp
vector<int> arr(100, 0);
for (size_t i = 0; i != arr.size(); ++i){
    // do something with arr[i]
}
```

# How to loop backward

## The pitfall

Looping backward through a vector when you declare the index as unsigned type
has pitfalls that we should avoid. Unfortunately, I have fallen into this trap
several times. Consider the following code:

```cpp
for (size_t i = arr.size()-1; i >= 0; --i){
    // do something with arr[i]
}
```

This code has a serious bug: because `i` is unsigned type, so `i` will never be
smaller than 0, as a result, the for loop never stops.

## The correct way

There are two ways to loop backward correctly, the first one is:

```
for (size_t i = arr.size()-1; i != (size_t)-1; --i){
    // do something with arr[i]
}
```

Because `i` is unsigned type, [when `i` equals 0, `i-1` will become `(size_t)-1`](https://stackoverflow.com/q/7221409/6064933),
which is the largest value that `size_t` can represent. Then the loop will stop
correctly.

The second one is:

```cpp
for (size_t i = arr.size(); i-- > 0; ){
    // do something with arr[i]
}
```

When `i` equals 0, the condition `i-- > 0` will not be met, so the loop will
terminate.

Ok, that is all I want to write in this post. Hope it can help you.

# References

+ [Loop backward, second way](https://softwareengineering.stackexchange.com/a/104680/276759).
+ [Loop forward](https://stackoverflow.com/q/6578048/6064933).(There are discussions why you should not ignore warnings about comparison between signed and unsigned type)
+ [Possible danger of using unsigned index in C++](https://eli.thegreenplace.net/2010/06/11/the-perils-of-unsigned-iteration-in-cc).
+ [Another discussion about the pitfalls](http://www.soundsoftware.ac.uk/c-pitfall-unsigned).
+ [Unsigned type, useful but tricky](http://www.drdobbs.com/cpp/unsigned-arithmetic-useful-but-tricky/240001198).
+ [How to loop forward and backward for a vector](https://stackoverflow.com/q/409348/6064933).
+ [How to find the maximum value of `size_t`](https://stackoverflow.com/q/409348/6064933).
