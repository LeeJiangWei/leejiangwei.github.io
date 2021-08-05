---
title: 验证回文串
categories:
  - LeetCode
  - 初级算法
  - 字符串
tags:
  - C++
  - 字符串
  - 双指针
index_img: /img/code.jpg
sticky: -10
date: 2021-08-05 21:15:06
---

# 验证回文串

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xne8id/

https://leetcode-cn.com/problems/valid-palindrome/

## 双指针法

左右指针，移动到合法位置（只能是字母、数字）后比较字符是否相同。

时间复杂度：`O(n)`

空间复杂度：`O(1)`

```c++
bool isPalindrome(string s) {
    int l = 0, r = s.length() - 1;
    while (l < r) {
        if (!isalnum(s[l])) {
            ++l;
            continue;
        }
        if (!isalnum(s[r])) {
            --r;
            continue;
        }
        if (tolower(s[l]) != tolower(s[r])) 
            return false;

        ++l;
        --r;
    }
    return true;
}
```

## 筛选 + 判断

比较蠢的方法，就不谈了。

