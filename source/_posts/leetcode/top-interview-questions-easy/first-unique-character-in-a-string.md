---
title: 第一个唯一字符
categories:
  - LeetCode
  - 初级算法
  - 字符串
tags:
  - C++
  - 哈希表
  - 队列
  - 字符串
index_img: /img/code.jpg
sticky: -10
date: 2021-08-04 21:26:55
---

# 第一个唯一字符

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xn5z8r/

https://leetcode-cn.com/problems/first-unique-character-in-a-string/

## 哈希表储存出现次数

第一次遍历用哈希表统计出现次数，第二次遍历找到第一个出现次数为 1 的字符。

时间复杂度：`O(n)`

空间复杂度：`O(|Σ|)` （需要最大为词表大小的空间来储存哈希表）

```c++
int firstUniqChar(string s) {
    unordered_map<char, int> m;

    for (char c: s) {
        m[c] += 1;
    }

    for (int i = 0; i < s.length(); ++i) {
        if (m[s[i]] == 1) return i;
    }
    
    return -1;
}
```

## 哈希表储存出现的索引

第一次遍历用哈希表储存索引，如果已经存在，就将索引值设为 -1 。第二次遍历，找出索引值最小的，返回。

时间、空间复杂度与上一个方法相同。

## 哈希表储存出现索引 + 队列

用和方法二一样的哈希表储存索引值，用一个队列来跟踪哪些字符是只出现一次的。

在插入字符时，如果不存在（第一次出现），将插入的字符及其索引加入到队列尾。而如果插入了一个已经出现过的字符时，从头开始检查队列，如果头元素出现过多次（在哈希表中对应的值为 -1），就弹出。

其实就是使用队列来维持字符原本的顺序，如果队列头的字符出现了多次就踢掉。

```c++
int firstUniqChar(string s) {
    unordered_map<char, int> position;
    queue<pair<char, int>> q;
    int n = s.size();
    for (int i = 0; i < n; ++i) {
        if (!position.count(s[i])) {
            position[s[i]] = i;
            q.emplace(s[i], i);
        }
        else {
            position[s[i]] = -1;
            while (!q.empty() && position[q.front().first] == -1) {
                q.pop();
            }
        }
    }
    return q.empty() ? -1 : q.front().second;
}
```

时间复杂度：遍历字符串 `O(n)` + 队列操作 `O(|Σ|)`  = `O(n)`

空间复杂度：`O(|Σ|)`
