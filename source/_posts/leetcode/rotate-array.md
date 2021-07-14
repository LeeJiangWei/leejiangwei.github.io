---
title: 旋转数组
date: 2021-07-12 18:46:48
categories:
- LeetCode
- 初级算法
tags:
- 数组
- C++
index_img: /img/code.jpg
---

# 旋转数组
https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x2skh7/
https://leetcode-cn.com/problems/rotate-array/

## 临时数组
最简单的方法，但是空间复杂为`O(n)`。

## 环状替换
如果直接将每个数字放至它**最后的位置**，这样被放置位置的元素会被覆盖从而丢失。

解决方法：从位置 0 开始，将它放到正确的位置（向右 k 位），然后**把被覆盖的元素保存下来**，再将它放到正确的位置（再向右 k 位），循环往复。

容易发现，当回到初始位置 0 时，有些数字可能还没有遍历到（当 nums.length % k = 0 时，只会遍历偶数位置然后回到开头），此时应该跳到下一个位置然后继续。至于怎么判断该不该跳，我还不是很懂，详见[题解](https://leetcode-cn.com/problems/rotate-array/solution/xuan-zhuan-shu-zu-by-leetcode-solution-nipk/)。

## 数组翻转
![](https://pic.leetcode-cn.com/1610072480-yiAYFw-image.png)
```c++
class Solution {
public:
    void reverse(vector<int>& nums, int start, int end) {
        while (start < end) {
            swap(nums[start], nums[end]);
            start += 1;
            end -= 1;
        }
    }
    void rotate(vector<int>& nums, int k) {
        k %= nums.size();
        reverse(nums, 0, nums.size() - 1);
        reverse(nums, 0, k - 1);
        reverse(nums, k, nums.size() - 1);
    }
};
```