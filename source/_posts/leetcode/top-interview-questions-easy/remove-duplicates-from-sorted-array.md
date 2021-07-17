---
title: 删除排序数组中的重复项
date: 2021-06-15 15:53:08
categories:
- LeetCode
- 初级算法
- 数组
tags:
- 数组
- 双指针
- C++
index_img: /img/code.jpg
---

# 删除排序数组中的重复项
https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x2gy9m/
https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/

## C++基础

### 迭代器
```c++
vector<int> a = {1, 2, 3};
auto iter_first = a.begin();
auto iter_second = iter_first + 1; // 可以直接对迭代器加减，得到指向下一个位置的迭代器
auto iter_last = a.end();

int str = *iter_first;             // 解引用迭代器得到它指向的值
```

### 遍历`vector`容器
范围`for`循环
```c++
vector<int> nums = {1, 2, 3};

for (auto &v: nums) {
    v *= 2;
}
```
`while`循环
```c++
vector<int> nums = {1, 2, 3};
auto current = nums.begin();

while (current != nums.end()) {    // 使用!=号来比较迭代器，>或<号不一定都有实现
    ++current;                     // 使用前置++来递增迭代器
}
```

## 题解

### 双指针法
使用左右两个指针，前后两两比较，在重复值的位置替换成不重复的值。
```c++
int removeDuplicates(vector<int>& nums) {
	if (nums.size() < 2) return nums.size();

	int j = 0;
	for (int i = 1; i < nums.size(); i++) // i是右指针，j是左指针
		if (nums[j] != nums[i]) nums[++j] = nums[i];
	return ++j;
}
```
右指针一直右移，遇到和左指针不同的，就将它的值赋给左指针，并使左指针移动到下一位。

### 我的解法
遇见重复的元素就用`erase()`直接删除了，也是太简单粗暴了，感觉智商被题解碾压了。
```c++
int removeDuplicates(vector<int>& nums) {
    if (nums.size() <= 1) {
        return nums.size();
    }

    auto curr = nums.begin() + 1;

    while (curr != nums.end()) {
        if (*curr == *(curr-1)) {
            curr = nums.erase(curr);
        } else {
            ++curr;
        }
    }

    return nums.size();
}
```
注意增加或删除vector中的元素会改变end迭代器的值，所以`nums.end()`需要写在循环条件里，每次循环都调用。

### 使用标准库函数`std::unique()`（作弊）
`std::unique()`接受一对迭代器，将这两个迭代器范围内的元素不重复的排到前面，然后返回不重复区域的下一个位置的迭代器。最后用`erase()`将此位置到末尾的元素都删除即可。
```c++
int removeDuplicates(vector<int>& nums) {
    nums.erase(std::unique(nums.begin(), nums.end()), nums.end());
    return nums.size();
}
```
