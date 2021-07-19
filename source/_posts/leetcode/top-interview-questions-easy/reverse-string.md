---
title: 反转字符串
categories:
  - LeetCode
  - 初级算法
  - 字符串
tags:
  - C++
  - 字符串
index_img: /img/code.jpg
sticky: -10
date: 2021-07-19 17:58:39
---

# 反转字符串

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnhbqj/

https://leetcode-cn.com/problems/reverse-string/

## 直接交换

```c++
void reverseString(vector<char>& s) {
    int n = s.size();
    for (int i = 0; i < n/2 ; ++i) {
        swap(s[i], s[n-1-i]);
    }
}
```

## 双指针

官方题解是双指针，其实是差不多的

```c++
void reverseString(vector<char>& s) {
    int n = s.size();
    for (int left = 0, right = n - 1; left < right; ++left, --right) {
        swap(s[left], s[right]);
    }
}
```

