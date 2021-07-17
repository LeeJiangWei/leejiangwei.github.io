---
title: 移动零
categories:
  - LeetCode
  - 初级算法
  - 数组
tags:
  - C++
  - 数组
  - 双指针
index_img: /img/code.jpg
date: 2021-07-15 14:36:03
---

# 移动零

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x2ba4i/

https://leetcode-cn.com/problems/move-zeroes/

## 双指针法

使用左右两个指针，右指针一直移动，当指向的数非0时，与左指针交换，并移动左指针一次。

+ 左右指针一开始会在一起，如果当前的数非 0 ，它们交换也不会有变化
+ 当遇到为 0 的数时，左指针停在 0 上，右指针会继续走，并将另一个非 0 的数交换过来

**不会存在这样的情况**：左右指针都非 0，交换导致相对顺序改变，因为右指针只会在遇到 0 的时候超越左指针，即左指针要么和右指针在一起，要么左指针指的是 0。

```c++
void moveZeroes(vector<int>& nums) {
    int left = 0, right = 0, n = nums.size();

    while (right < n) {
        if (nums[right] != 0) {
            swap(nums[left], nums[right]);
            ++left;
        }
        ++right;
    }
}
```

