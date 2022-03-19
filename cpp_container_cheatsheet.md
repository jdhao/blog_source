---
title: "Cpp Container/Adapter and Common Structure CheatSheet"
date: 2021-07-04 19:02:50+0800
tags: [C++]
categories: [Note]
---

I use Python in my work, and rarely need C++. So I need to compile this C++
cheatsheet for common STL containers/adapters just in case I need them.

<!--more-->

# vector

+ Define a 1D vector: `vector<int> arr = {1, 2, 3};`
+ Define a 2D vector:  `vector<vector<int>> mat = {{1, 2, 3}, {4, 5}};`
+ Define a 2D vector of size 3x4: `vector<vector<int> > mat(3, vector<int>(4, 0));`
+ Add an element: `arr.push_back(2);`
+ Insert an element: `arr.insert(arr.begin(), -1);`
+ Insert another vector: `vector<int> another = {1, 2, 3}; arr.insert(arr.begin(), another.begin(), another.end());`
+ Check its size: `cout << arr.size();`
+ Check if vector is empty: `cout << arr.empty();`
+ Slice a vector, i.e., get a sub-vector from an existing vector: `vector<int> arr(10, 1); vector<int> new_vec(arr.begin(), arr.begin()+3);`
  (this is end-exclusive, so the resulting vector has 3 elements)
+ Sort a vector:
    + In ascending order (the default): `std::sort(arr.begin(), arr.end());`
    + In descending order (we need to use custom compare function): `std::sort(arr.begin(), arr.end(), std::greater<int>());`

Ref:

+ Best way to extract a sub-vector from a vector?: https://stackoverflow.com/q/421573/6064933

# string

+ Define a string: `string myStr = "hello";`
+ Get string size: `myStr.size()`
+ Get sub-string: `myStr.substr(0, 2)` (Get the substring starting at index 0 and with length 2, end-exclusive style)
+ Get sub-string: `myStr.substr(1)` (Get substring starting at index 1 till the end)
+ Check if string is empty: `myStr.empty();`
+ Generate a string with `n` same character `ch`: `string(n, ch)`;
+ Reverse a string: use `std::reverse`, `std::reverse(myStr.begin(), myStr.end())`.

Ref:

+ Repeat string n times: https://stackoverflow.com/q/166630/6064933

# set (keys have order)

Typically implemented using balanced binary search tree.

+ Define a set: `set<int> x = {1, 2, 3};`
+ Add an element: `x.insert(5);`
+ Check size: `x.size()`
+ Check if set is empty: `x.empty();`
+ Check if a key exists in a set: `x.find(2) != x.end();` or `x.count(2) != 0`
+ Initialize set from a vector of int: `vector<int> arr = {1, 2, 1, 3}; set<int> my_set(arr.begin(), arr.end());`

There is also `unordered_set`, which is different from `set`:

+ The underlying implementation for `set` is binary search tree (BST), while the underlying implementation for `unordered_set` is hash table.
+ The element in `set` is sorted so that their order is guaranteed, while for `unordered_set`, there is no guarantee for the element order.
+ `set` requires less memory than `unordered_set` (`unordered_set` has to store a hash table).
+ Search time is at most `O(log(n))` for `set`, while for `unordered_set`, the search time is on average `O(1)`, but may be `O(n)` (highly unlikely)

# map (works like a Python dict)

Typically implemented using balanced binary search tree.

+ Define a map: `map<int, string> items;`
+ Add an element: `items.insert({1, "apple"});` or `items[1] = "apple";`
+ Remove an element: `items.erase("apple");`
+ Access a key (no check): `cout << items[1] << endl;` (we have to make sure the key exists, otherwise it will be inserted silently)
+ Access a key (with check): `cout << items.at(4);` (will error out)
+ Show map element key and value:

  ```cpp
  for (auto item: items){
    cout << item.first << " " << item.second << endl;
  }
  ```

+ Check if a key exists in a map: `bool has_key = items.find(2) != items.end();` or `items.count(2) != 0`

There is also `unordered_map`. The different between map and unordered_map is similar to difference between set and unordered_set.
So we will not elaborate on this.

Note that when map is defined using the `const` modifier, you can not access map element with operator `[]`,
since it is possible to alter map value when accessing map element like this,
check [this post](https://stackoverflow.com/a/5134691/6064933) for more details.

# queue

Queue is a data structure that provides FIFO (first in first out) feature.
Element that is added first will also be removed first.

+ Get queue size: `queue<int> q; cout << q.size() << endl;`
+ Check if queue is empty: use `q.empty()` or `q.size() == 0`
+ Get front element of queue: `cout << q.front() << endl;`
+ Add element to queue: `q.push(4);`
+ Pop up front element: `q.pop();`

# stack

Stack is the opposite of queue. It provides a LIFO (last in first out) data structure.
Element that is added last will be removed first.

+ Get stack size: `stack<int> s; cout << s.size() << endl;`
+ Check if stack is empty: `s.empty()` or `s.size() == 0`
+ Get top element from stack: `cout << s.top() << endl;` (note that getting top element when **stack is empty** leads to errors!)
+ Add element to stack: `s.push(1);`
+ Pop up element from stack: `s.pop();`

# tuple

[Tuple](https://en.cppreference.com/w/cpp/utility/tuple) can be used to different types of data.

+ Create a tuple: `std::make_tuple("abc", 2, 3);`
+ Get tuple element: `std:get<0>(some_tuple);` (get first element, **really weird and unintuitive syntax!**)

# pair

Pair can be used to hold two different data types conveniently.

+ Create a pair: `pair<int, string> my_pair = std::make_pair(2, "abc")`
+ Get pair element: `my_pair.first` and `my_pair.second`

Ref:

+ Get tuple element: https://stackoverflow.com/q/32606464/6064933

# References

+ Sort vector in descending order: https://stackoverflow.com/q/9025084/6064933
+ Why would anyone use set instead of unordered_set?: https://stackoverflow.com/q/1349734/6064933
+ How to choose between map and unordered_map?: https://stackoverflow.com/q/13799593/6064933
+ Is there any advantage of using map over unordered_map in case of trivial keys?: https://stackoverflow.com/q/2196995/6064933
