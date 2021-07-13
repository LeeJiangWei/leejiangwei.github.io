---
title: 只出现一次的数字
date: 2021-07-14 00:41:10
categories:
- LeetCode
- 初级算法
tags:
- 数组
- 哈希表
- 位运算
- C++
index_img: /img/code.jpg
---

# 只出现一次的数字

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x21ib6/

https://leetcode-cn.com/problems/single-number/

## 哈希表

使用哈希表，如果放入元素时候已经有了，就删掉它。最后剩下的就是只出现一次的元素。时间复杂度是`O(n)`，空间复杂度也是`O(n)`。

```c++
int singleNumber(vector<int>& nums) {
    unordered_set<int> s;
    for (int x: nums) {
        if (s.count(x)) {
            s.erase(x);
        } else {
            s.insert(x);
        }
    }
    return *(s.begin());
}
```

## 位运算

数字按位进行异或运算。基于以下几点事实：

+ 任何数和自己做异或运算，结果为 0，即 a ⊕ a = 0 。
+ 任何数和 0 做异或运算，结果还是自己，即 a ⊕ 0 = 0 。
+ 异或运算中，满足交换律和结合律，a ⊕ b ⊕ a = b ⊕ a ⊕ a = b ⊕ ( a ⊕ a ) = b ⊕ 0 = b 。

```c++
int singleNumber(vector<int>& nums) {
    int ret = 0;
    for (auto e: nums) ret ^= e;
    return ret;
}
```

## C++的位和逻辑运算操作符

| 运算        | 位运算操作符 (bitwise) | 逻辑运算符(logical) |
| ----------- | ---------------------- | ------------------- |
| AND         | &                      | &&                  |
| OR          | \|                     | \|\|                |
| XOR         | ^                      |                     |
| NOT         | ~                      | !                   |
| left shift  | <<                     |                     |
| right shift | >>                     |                     |

参考链接：https://www.geeksforgeeks.org/bitwise-operators-in-c-cpp/



