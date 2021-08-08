---
title: 小米 2018 校招算法工程师编程之字符串匹配
date: 2017-09-21 22:07:03 +0800
tags: [C++, string, algorithms]
categories: [Programming]
---

这是一道小米 2018 校园招聘算法工程师的编程题目，类似与正则表达式匹配，但是该题
不能使用 Python，因此需要自己实现这个简单的匹配，题目如下：<!--more-->


>给出两个字符串 `text` 和 `pattern`, 两个字符串均由小写字母构成，找出 `text` 中匹配 `pattern` 的最短子字符串的起始以及终止的索引号码，所谓匹配，是指 `pattern` 中字符都出现在该子字符串，并且顺序不变，这些字符中间可以有其他字符出现，不要求连续。
>样例输入格式为两个字符串，用空格隔开，输出为最短的匹配字符串的前后索引，用空格隔开，如果不存在这样的字符串，那么输出 -1， -1.  
>样例输入：  
>axxxbcaxbcaxxbc abc  
>abcd x  
>axxxbaxbab ab  
>样例输出：  
>6 9  
>-1 -1  
>8 9  

这道题目的思路是依次遍历 `text`, 如果 `pattern` 的第一个字符被找到，那么从当前
位置的下一个位置开始遍历 `text` 找 `pattern` 的第二个字符，重复这个过程，直到
`pattern` 的最后一个字符被找到，或者遍历 `text` 也未找到 `pattern` 中所有字符串
。 每找到这样的一个子字符串，就记录该字符串前后的索引，如果子字符串长度等于
`pattern`，此时我们已经找到最短匹配，停止继续搜索；如果从 `text` 某个位置开始，
未找到匹配 `pattern` 的子字符串，那么该位置以后也不可能会有，停止搜索。

我们可以使用递归的代码来解决这个问题，也可以使用循环代码，递归方式的代码如下：

```cpp
#include <bits/stdc++.h>
using namespace std;

string text, pattern;
// posInPattern mean the next search character in pattern
// posInText means the next pos the search start
int dfs(int posInPattern, int posInText){
    if (posInPattern == (int)pattern.size()){
        return posInText-1;
    }
    
    int endID = -1;
    for (int i = posInText; i != (int)text.size(); ++i){
        if (text[i] == pattern[posInPattern]){
            endID = dfs(posInPattern+1, i+1);
            break;
        }
    }
    return endID;
}

int main(){
    cin >> text >> pattern;

    int lenText = (int)text.size();
    int lenPattern = (int)pattern.size();

    vector<pair<int, int>> records;
    for (int i = 0; i <= lenText-lenPattern; ++i){
        if (text[i] == pattern[0]){
            int end = dfs(1, i+1);
            if (end != -1){
                records.push_back(make_pair(i, end));
            }// if no matching substring are found starting from current 
            //position, then no substring can be found after this position.
            else{
                break;
            }
        }
    }

    if (records.empty()) {// if no matching substring is found
        cout << "-1 -1\n";
    }
    else{ // if mathcing substring is found, then select the shortest one.
        int begin = records[0].first, end = records[0].second;
        for (auto it = records.begin()+1; it != records.end(); ++it){
            if (it->second - it->first < end - begin){
                begin = it->first;
                end = it->second;
            }
        }
        cout << begin << " " << end << '\n';
    }
    
    return 0;
}
```

采用循环方式的代码如下：

```cpp
#include <bits/stdc++.h>
using namespace std;
int main(){
    string text, pattern;
    cin >> text >> pattern;
    int lenText = (int)text.size();
    int lenPattern = (int)pattern.size();
    vector<pair<int, int>> record;

    for (int i = 0; i <= lenText-lenPattern; ++i){
        if (text[i] != pattern[0]){
            continue;
        }
        else{
            int j = i+1; // next search position in text
            int k = 1; // next search character in pattern
            while (j < lenText && k < lenPattern){
                if (text[j] == pattern[k]){
                    ++k;
                }
                ++j;
            }
            // if we found the string
            if (k == lenPattern){
                record.push_back(make_pair(i, j-1));
            }
            else{ break;} // we cann't find any string after, so break
        }
    }
    if (records.empty()) {// if no matching substring is found
        cout << "-1 -1\n";
    }
    else{ // if mathcing substring is found, then select the shortest one.
        int begin = records[0].first, end = records[0].second;
        for (auto it = records.begin()+1; it != records.end(); ++it){
            if (it->second - it->first < end - begin){
                begin = it->first;
                end = it->second;
            }
        }
        cout << begin << " " << end << '\n';
    }

    return 0;
}

```
(全文完)
