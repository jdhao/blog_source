---
title: 几道算法问题解答
date: 2017-10-19 14:12:30 +0800
tags: [algorithms, Mobike]
categories: [Programming]
---

记录一下做的几道算法问题的解答。<!--more-->

# 第一题

> 给出两个字符串 A 和 B，B 的长度大于等于 A（B 的长度不超过 100），在 A 的开头或者结尾添加字符，使得 A 与 B 长度一样，当两者长度一样时，A 与 B 不相同字符的个数最小为多少？例如 A 是 "be", B 是 "abd", 可以在 A 的开头增加字符 'a'，此时 A 与 B 不相同字符数最小，为 1

想法：因为只能给 A 增加字符，所以固定 B，采用 sliding window 的方式滑动 A，计算
A 与 B 的最大相同的字符数目 M，所求的答案就是 A 的长度减去 M.

完整代码如下：
```cpp
#include <iostream>
#include <string>

using namespace std;

int main(){
    string A, B;
    cin >> A >> B;

    int lenA = (int)A.size();
    int lenB = (int)B.size();

    int minVal = 101;
    for (int i = 0; i <= lenA - lenB; ++i){
        int equalNum = checkEqualNum(A, B.substr(i, lenA));
        if (lenA - equalNum < minVal){
            minVal = lenA - equalNum;
        }
    }

    cout << minVal << endl;
}
```

# 第二题

>给出任意一个整数数组（可能包含重复数字），每次只能对数组进行一个操作：取出一个元素，放到数组末尾。最少需要操作多少次，该数组能够变成一个排好序的数组。
>例子
>输入数组为 `{3, 4, 1, 2}`，输出应该是 2
>解释：先把 3 移到数组最后，再把 4 移到数组最后，排序完成。操作 2 次。

想法：假设数组已经排号序，原数组中有一部分元素相对位置在排序好的数组中仍然不变
，这部分元素不用移动，只用移动剩余元素即可。例如上述的例子，1 和 2 两个元素在原
数组中已经是有序的，其实排序的过程就是把 3 和 4 移动到数组末尾。因此可以把数组
排序，然后计算排序号的数组和原数组有多少元素相对位置是正确的，剩余元素数目就是
最小的操作次数。

```cpp
#include <vector>
#include <iostream>
using namespace std;

int main(){
    int N; // N 是数组的大小
    cin >> N;
    
    vector<int> nums(N);
    vector<int> nums_copy(N);
    
    for (int i = 0; i != N; ++i){
        cin >> nums[i];
        nums_copy[i] = nums[i];
    }
    
    sort(nums_copy.begin(), nums_copy.end());

    size_t j = 0;
    for (size_t i = 0, end = nums.size(); i != end; ++i){
        if (nums[i] == nums_copy[j])
            ++j;
    }
    cout << nums.size() - j << endl;
    return 0;
}
```

该方法的时间复杂度 $O(n\log(n))$，空间复杂度 $O(n)$. 还有一种时间复杂度和空间复
杂度更低的方法，但是需要考虑的更加细致，可以参考这里的[讨论
](https://goo.gl/7FK1b5).

（全文完）
