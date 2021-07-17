---
title: 加一
categories:
  - LeetCode
  - 初级算法
  - 数组
tags:
  - C++
  - 数组
index_img: /img/code.jpg
date: 2021-07-14 21:13:00
---

# 加一

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x2cv1c/

https://leetcode-cn.com/problems/plus-one/

## 严密的逻辑

考虑所有可能的情况即可：

1. 不用进位，直接加一
2. 需要进位，非最高位，则置为 0 并执行高一位的操作
3. 需要进位，是最高位，则置为 0 并在前面插入一个 1，直接返回

```c++
vector<int> plusOne(vector<int>& digits) {
    int curr = digits.size() - 1;

    for (curr; curr >= 0; --curr) {
        if (digits[curr] + 1 == 10) {
            digits[curr] = 0;
            if (curr == 0) {
                digits.insert(digits.begin(), 1);
                return digits;
            }
        } else {
            digits[curr] += 1;
            break;
        }
    }

    return digits;
}
```

