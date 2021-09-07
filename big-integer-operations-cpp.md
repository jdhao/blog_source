---
title: Some Programming Questions Related to Operations with Big Integers
date: 2017-09-21 10:23:46 +0800
tags: [algorithms]
categories: [Programming]
---

The C++ standard library doesn't support big integer operations such as
addition and multiplication. In this post, I will show how to solve such
problems in C++.

<!--more-->

# Big integer addition

The built in integer types in C++ are mainly `short`, `int`, `long` and `long
long`. These types can only represent relatively small integers. If we store
big integers in these types, [integer
overflow](https://en.wikipedia.org/wiki/Integer_overflow) may happen. As a
solution, we can store big integers as strings and do integer addition the same
way as we do in paper.

The following code accepts two integers represented as strings and return their
sum as a string,

```cpp
string bigIntAddition(const string& num1, const string& num2){
    string result;
    int i = static_cast<int>(num1.size() - 1);
    int j = static_cast<int>(num2.size() - 1);
    int carry = 0;
    while (i >= 0 or j >= 0 or carry != 0){
        int sum = (i < 0 ? 0 : num1[i--] - '0') + (j < 0 ? 0 : num2[j--] - '0') + carry;
        result += ((sum % 10) + '0');
        carry = sum / 10;
    }
    std::reverse(result.begin(), result.end());

    return result;
}
```

# Big integer multiplication

Integer multiplication is bit more complex, if two integers have $m$ and $n$
digits, then [the final result have a maximum of $m +
n$](https://math.stackexchange.com/questions/857424/estimate-or-calculate-the-number-of-digits-of-a-multiplication)
digits. [One post on
leetcode](https://discuss.leetcode.com/topic/30508/easiest-java-solution-with-graph-explanation)
gives an excellent explanation of how to do mulitiplication manually, digit by
digit. The basic idea is this

> Start from the least significant digit of both
integers, and calculate their product. If the indices of the digits are $i$,
$j$, then the product will be added to indices $i+j$ and $i+j+1$.

This is a repeated process where each digit in the result is gradually built by
addition. Until we finished the process, the digits are changing their values
continuously.

Following is the code to do interger(expresses as string) multiplications,

```cpp
string bigIntMultiplication(const string& num1, const string& num2){
    if (num1 == "0" or num2 == "0"){
        return "0";
    }

    int N = static_cast<int>(num1.size());
    int M = static_cast<int>(num2.size());

    vector<int> result(N+M, 0);
    for (int i = N-1; i >= 0; --i){
        for (int j = M-1; j >= 0; --j){
            int tmp = (num1[i] - '0') * (num2[j] - '0');
            int sum = tmp + result[i+j+1];
            result[i+j+1] = sum % 10;
            result[i+j] += sum / 10;
        }
    }   
    // in case the result only have N+M-1 digits or one of num is zero
    ostringstream out;
    int i = 0;
    while (result[i] == 0) {++i;}
    for (; i != N+M; ++i){
        out << result[i];
    }

    return out.str();
}
```

# Factorial of n

Factorial increases really fast, for example, the factorial of `100!` has 158
digits. In order to represent such big integer, we can use an array to store
its digit separately. Below is an algorithm to find the factorial of `n`,

1. create an array `res[N]` big enough to store all the digits in `n!`,
   initialize `res[0]` to 1

2. let `m` be the number of digits that current result takes up, the initialize
   `m` to be 1

3. for `num` from 2 to n    
    1. initialize `carry` to 0
    2. for each digit in current result (i.e., from res[0] to res[m-1]),
    let cur = num*res[j] + carry, put last digit of cur in res[j], put other
    digit in cur in carry.
    3. if carry is not zero, put every digit in carry into res by incresing m

In order to simplify computation, we store all the digits of the result in
reverse order, which means that the least significant digit is stored in the
smallest index of the array, kind of like ["small
endian"](https://en.wikipedia.org/wiki/Endianness). In each iteration, we also
keep track of how many digits the current result takes up. The following code
is an implementation of this idea,

```cpp
string factorial(int n){
    const int N = 10000;
    int* digits = new int[N];
    digits[0] = 1;

    int m = 1; 
    for (int num = 2; num <= n; ++num){

        // multipy num and every digit in current result
        int carry = 0;
        for (int j = 0; j != m; ++j){
            int cur += num*digits[j];
            digits[j] = cur % 10;
            carry = cur / 10;
        }
        // if carry is not zeros, span current result using digits in carry
        while (carry){
            digits[m++] = carry % 10;
            carry /= 10;
        }
    }

    ostringstream out;
    for (int i = m-1; i >= 0; --i){
        out << digits[i];
    }
    delete[] digits;

    return out.str();
}
```

# References

1. [Leetcode, add two strings.](https://leetcode.com/problems/add-strings/description/)
2. [Leetcode, string multiplication.](https://leetcode.com/problems/multiply-strings/description/)
3. [Calculating factorials of big intergers.](https://geekfactorial.blogspot.com/2016/08/factorial-of-large-numbers-in-c-or-cpp.html)
