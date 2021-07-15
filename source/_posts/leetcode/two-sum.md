---
title: 两数之和
categories:
  - LeetCode
  - 初级算法
tags:
  - C++
  - 数组
  - 哈希表
index_img: /img/code.jpg
date: 2021-07-15 15:18:19
---

# 两数之和

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x2jrse/

https://leetcode-cn.com/problems/two-sum/

## 暴力法

双重循环遍历，时间复杂度显然是`O(n^2)`

## 哈希表

在暴力法中，对于每一个`x`，都需要遍历一次整个数组来寻找`target - x`。

如何加快`target - x`的寻找速度？答案就是使用哈希表来储存`target - x`的值，这样寻找`target - x`的时间复杂度只有`O(1)`，总时间复杂度为`O(n)`。

```c++
vector<int> twoSum(vector<int>& nums, int target) {
    unordered_map<int, int> m;
    for (int i = 0; i < nums.size(); ++i) {
        auto p = m.find(nums[i]);
        if (p == m.end()) {
            m.insert({target-nums[i], i});
        } else {
            return {i, p -> second};
        }
    }
    return {};
}
```

#### 注：

这里使用了 C++ 11的新特性列表初始化 (initialiser_list)，可以用花括号来初始化vector、作为函数参数使用、直接返回。例如：

```c++
// 初始化
std::vector<std::string> words = {"the", "mortar", "holding", "code", "together"}; 

twoSum({1, 3, 4, 5}, 7); // 作为参数	

std::vector<int> numbers() {
    return {0, 1, 2, 3, 4, 5};  // 作为返回值
}
```

