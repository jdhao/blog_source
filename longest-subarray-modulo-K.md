---
title: "Find Longest Subarray Whose Sum Is Divisible by K"
date: 2017-09-01 00:05:38 +0800
tags: [algorithms]
categories: [Programming]
---

The problem is as follows:

> Given an array $A$ of length $N$, $A[i] \ge 0$ ( $0 \le i \le N-1$) , and a
> number $K$. Find the longest subarray whose sum is divisible by $K$, if there
> are no such subarray, return 0;

<!--more-->

The idea is to traverse the array and record the result of cumulative sum
modulo $K$ and its corresponding index. If for index $i, j$, the remainder is
the same, then the subarray between $i, j$ must be a valid subarray (i.e., its
sum is divisible by $K$). In order to find the longest subarray, we can keep a
list of index corresponding to each remainder (there are $K$ remainders, $0, 1,
2, ..., K-1$). During the traversing process, we can also easily find the
longest subarray (index list of each remainder is stored in ascending order).

Below is the implementation of the above idea:

```cpp
int findLongestSubarray(vector<int>& nums){
    int N = nums.size();
    map<int, vector<int>> indexInfo;

    index[0].push_back(-1);

    // the remainder at current index
    int cur_remainder = 0;
    int maxLen = 0;

    for (int i = 0; i < N; i++) {
        cur_remainder = (cur_remainder + nums[i]) % K;

        if (indexInfo[cur_remainder].size() != 0){
            maxLen = max(maxLen, i - indexInfo[cur_remainder].front());
        }

        indexInfo[cur_remainder].push_back(i);
    }

    return maxLen;
}
```

Note that the line `index[0].push_back(-1);` is needed.
Otherwise, for $A = [3]$ and `$k=3$`, we can not the right answer.

Time complexity: traversing the array and finding the maxLen during the process
takes $O(n)$ time. Space complexity is apparently $O(n)$.

P.S. Thanks to [Scott](https://disqus.com/by/disqus_E0q5yo3A2a/) for pointing
out that there is no need to sort the list corresponding to each remainder,
because they are already in sorted order.

# Reference

+ [A blog here describing the algorithm](http://www.zrzahid.com/subarray-with-sum-divisible-by-k/)
