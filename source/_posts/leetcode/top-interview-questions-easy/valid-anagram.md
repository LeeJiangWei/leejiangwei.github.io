---
title: 有效字母异位词
categories:
  - LeetCode
  - 初级算法
  - 字符串
tags:
  - C++
  - 字符串
  - 哈希表
index_img: /img/code.jpg
sticky: -10
date: 2021-08-04 21:51:53
---

# 有效字母异位词

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xn96us/

https://leetcode-cn.com/problems/valid-anagram/

## 哈希表

用一个哈希表储存字母出现次数。在字符串 `s` 中出现的 +1，在 `t` 中出现的 -1，最后看是不是全为 0 即可。

时间复杂度：`O(n)`

空间复杂度：`O(|Σ|)`

```c++
bool isAnagram(string s, string t) {
    unordered_map<char, int> m;

    for (char &c: s) {
        m[c] += 1;
    }

    for (char &c: t) {
        m[c] -= 1;
    }

    for (auto curr = m.begin(); curr != m.end(); ++curr) {
        if (curr -> second != 0) return false;
    }
    return true;
}
```

## 排序后比较

排序后比较字符串是否相等。

时间复杂度：快速排序平均复杂度为 `O(nlogn)`

空间复杂度：排序所需要的空间为 `O(logn)`