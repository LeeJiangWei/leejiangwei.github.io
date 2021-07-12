---
title: 存在重复元素
date: 2021-07-12 22:01:45
categories:
- LeetCode
- 初级算法
tags:
- 数组
- 哈希表
- C++
index_img: /img/code.jpg
---

# 存在重复元素
https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x248f5/
https://leetcode-cn.com/problems/contains-duplicate/

## 排序后比较

先排序，然后比较相邻元素即可。使用C++的排序泛型算法`sort()`。

```c++
bool containsDuplicate(vector<int>& nums) {
    if (nums.size() <1) return false;
    sort(nums.begin(), nums.end());
    for (int i = 0; i < nums.size()-1; ++i) {
        if (nums[i] == nums[i+1]) return true;
    }
    return false;
}
```

## 哈希表

将元素插入哈希表，如果插入时发现重复了，就可以返回了。

C++中，类似的功能可以用关联容器`unordered_set`实现。

```c++
bool containsDuplicate(vector<int>& nums) {
    unordered_set<int> s;
    for (int x: nums) {
        if (s.find(x) != s.end()) {
            return true;
        }
        s.insert(x);
    }
    return false;
}
```

