---
title: "快排(quick sort) C++ 实现"
date: 2021-07-10T12:19:38+08:00
draft: false
tags: [sorting]
categories: [Programming]
---

快排是一种快速排序算法，原理是从数组中选择一个 pivot，数组中小于等于 pivot 的元素放到左边，大于 pivot 的元素放到右边。然后对左边和右边数组分别递归进行快速排序，最后整个数组就成为排序好的数组。

<!--more-->

# 非原地算法

下面是 quicksort 的一种实现方法，非原地操作，每次会申请额外的空间存放左子数组和右子数组，但是理解上非常简单，没有任何难理解的地方：

```cpp
#include <iostream>
#include <random>
#include <string>
#include <vector>
#include <ctime>

using std::random_device;
using std::cout;
using std::endl;
using std::vector;
using std::string;

void printList(vector<int>&, const string&);

vector<int> quickSort(vector<int> arr){
  if (arr.size() <= 1){
    return arr;
  }

  int N = arr.size();
  // arr[mid] 是 pivot
  int mid = (N-1) / 2;

  vector<int> left;
  vector<int> right;

  for (int i = 0; i < N; i++){
    if (i == mid) continue;

    // 对于原始数组，小于等于 pivot 的元素放左子数组，大于 pivot 的元素放右子数组
    if (arr[i] <= arr[mid]){
      left.push_back(arr[i]);
    }
    else{
      right.push_back(arr[i]);
    }
  }

  // 对左右子数组，再分别进行快排
  left = quickSort(left);
  right = quickSort(right);

  vector<int> new_arr;
  for (auto & x: left){
    new_arr.push_back(x);
  }
  new_arr.push_back(arr[mid]);
  for (auto & x: right){
    new_arr.push_back(x);
  }

  return new_arr;
}

vector<int> bubbleSort(vector<int> & arr){
  int N = arr.size();
  for (int i = 0; i < N; i++){
    for (int j = i+1; j < N; j++){
      if (arr[i] > arr[j]){
        // swap element
        std::swap(arr[i], arr[j]);
      }
    }
  }

  return arr;
}

// Generate a random sequence of length len, in range(low, high) (inclusive).
// need to #include<random>
vector<int> genRandom(int low, int high, int len){
  std::random_device rd;
  std::mt19937 gen(rd());
  std::uniform_int_distribution<int> distribution(low, high);

  vector<int> arr(len, 0);
  for (int i = 0; i != len; ++i){
      arr[i] = distribution(gen);
  }

  return arr;
}

void printList(vector<int>& arr, const string& desc){
  cout << desc << ": ";

  for (auto it = arr.begin(); it != arr.end(); it++){
    cout << *it << ((it != arr.end()-1) ? ' ' : '\n');
  }
}

int main()
{
  vector<int> arr = genRandom(1, 1000, 10);
  cout << "Before quick sort\n";
  printList(arr, "arr");

  auto arr1 = quickSort(arr);
  cout << "After quick sort\n";
  printList(arr1, "arr1");

  auto arr2 = bubbleSort(arr);
  cout << "After bubble sort\n";
  printList(arr2, "arr2");

  return 0;
}
```

快排实现一个坑，如果把数列中间元素作为 pivot，pivot 不能合并到左边的数组里面，否则会造成无限循环，pivot 必须单独拿出来。举个例子，假如数组为 `arr = {2, 1}`, pivot 是 arr[0],如果把小于等于 pivot 的元素放在左边数组，把大于 pivot 的元素放在右边数组，结果会是左边 {2, 1}, 右边 {}, 然后左右两边数组又调用 quickSort()，quickSort 终止条件是 `arr.size() <= 1`，左边的数组会继续重复上述的过程，永远循环下去。

正确的做法是把 pivot 单独拿出来，然后对左边数组进行 quickSort 递归，再对右边数组进行 quickSort 递归，然后把排序好的左数组，pivot，排序好的右数组合并起来。还以上面 case 为例，pivot 是 arr[0]，左边数组 {1}, 右边数组 {}, 左右数组都达到终止条件返回，然后把左数组，pivot，右数组拼起来，排序好的数组就是 {1, 2}。

# 原地算法

还有一种原地实现的算法，不需要开辟额外的空间，不过没有上面的实现那么直观容易理解。

```cpp
#include <iostream>
#include <vector>
#include <random>

using namespace std;

// Generate a random sequence of length len, in range(low, high) (inclusive).
// need to #include<random>
vector<int> genRandom(int low, int high, int len){
  std::random_device rd;
  std::mt19937 gen(rd());
  std::uniform_int_distribution<int> distribution(low, high);

  vector<int> arr(len, 0);
  for (int i = 0; i != len; ++i){
      arr[i] = distribution(gen);
  }

  return arr;
}

int partion(vector<int> &arr, int low, int high) {
  int pivot = arr[low];
  int i = low;

  for (int j = low+1; j < high; j++){
    if (arr[j] <= pivot){
      i++;
      swap(arr[i], arr[j]);
    }
  }
  // 经过上述 for 循环， i 以及 i 左边都是小于等于 pivot 的元素，最后把 arr[i] 和
  // pivot 元素位置调换一下即可。即可保证我们完成了 partion
  swap(arr[i], arr[low]);

  return i;
}
void quickSort(vector<int>& arr, int low, int high){
  // low < high，才需要排序
  if (low < high){
    int r = partion(arr, low, high);

    quickSort(arr, low, r);
    quickSort(arr, r+1, high);
  }
}

int main() {
  vector<int> arr = genRandom(1, 100, 5);

   for(auto e: arr)
        cout<< e <<" ";
    cout<< endl;

  int N = arr.size();
  quickSort(arr, 0, N);

   for(auto e: arr)
        cout<< e <<" ";
    cout<< endl;

  return 0;
}
```

上述实现中，最核心的是 partion() 函数，partion 函数选择第一个元素作为 pivot，然后把 pivot 移动到最终它应该在的位置，并保证该位置左边都是小于等于 pivot 的元素，右边都是大于 pivot 的元素，最后返回 pivot 元素在新数组中的位置。pivot 不一定非要选择第一个元素，也可以选择最后一个元素，逻辑对应改动即可。其实 pivot 也可以选择其他元素，不过实现起来感觉比较麻烦，想了很久，也没实现对，总有一些 edge case 没处理正确。

# 参考

+ https://stackoverflow.com/a/22505710/6064933
