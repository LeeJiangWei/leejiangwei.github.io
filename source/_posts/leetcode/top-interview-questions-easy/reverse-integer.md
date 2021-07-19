---
title: 整数反转
categories:
  - LeetCode
  - 初级算法
  - 字符串
tags:
  - C++
index_img: /img/code.jpg
sticky: -10
date: 2021-07-19 19:26:12
---

# 整数反转

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnx13t/

https://leetcode-cn.com/problems/reverse-integer/

## 逐位运算

使用`x % 10`取得个位数的值，将它加到临时值`ret`中，并且让`ret`倍增10。

使用`x /= 10`来去掉个位（C++）。

**用`INT_MIN / 10`和`INT_MAX / 10`来提前判断是否会越界**。

```c++
int reverse(int x) {
    int ret = 0;

    while (x != 0) {
        if (ret < INT_MIN / 10 || ret > INT_MAX / 10) {
            return 0;
        }
        int last = x % 10;
        x /= 10;
        ret = 10 * ret + last;
    }


    return ret;
}
```
