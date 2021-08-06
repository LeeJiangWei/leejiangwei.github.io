---
title: 字符串转换整数
categories:
  - LeetCode
  - 初级算法
  - 字符串
tags:
  - C++
  - 字符串
index_img: /img/code.jpg
sticky: -10
date: 2021-08-06 10:36:18
---

# 字符串转换整数 (atoi)

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnoilh/

https://leetcode-cn.com/problems/string-to-integer-atoi/

## 有限状态机 (FSM)

字符串处理的题目往往涉及复杂的流程以及条件情况，如果直接上手写程序，一不小心就会写出极其臃肿的代码。臃肿还是其次，一堆 if-else 各种边界条件严格的想吐。。

使用有限状态机实现各种状态之间的转移。根据题目，容易画出来状态机图：

![](https://i.loli.net/2021/08/06/3OLdx5pKkloamte.png)

状态机可以用一个状态转移表来实现。如图所示，行头是当前状态，列头是下一个输入，表中内容是跳转至的状态。直接将这个表编码进程序中，在状态为 in_number 时计数。

![](https://i.loli.net/2021/08/06/XTm849u6plGHdSt.png)

对于 C++，居然可以用 long long 类型来计数，再去判断它跟 INT_MAX 和 INT_MIN 谁大谁小，属实有点无语。自己写程序的时候搞了半天。

```c++
class Automaton {
    string state = "start";
    unordered_map<string, vector<string>> table = {
        {"start", {"start", "signed", "in_number", "end"}},
        {"signed", {"end", "end", "in_number", "end"}},
        {"in_number", {"end", "end", "in_number", "end"}},
        {"end", {"end", "end", "end", "end"}}
    };

    int get_col(char c) {
        if (isspace(c)) return 0;
        if (c == '+' or c == '-') return 1;
        if (isdigit(c)) return 2;
        return 3;
    }
public:
    int sign = 1;
    long long ans = 0;

    void get(char c) {
        state = table[state][get_col(c)];
        if (state == "in_number") {
            ans = ans * 10 + c - '0';
            ans = sign == 1 ? min(ans, (long long)INT_MAX) : min(ans, -(long long)INT_MIN);
        }
        else if (state == "signed")
            sign = c == '+' ? 1 : -1;
    }
};

class Solution {
public:
    int myAtoi(string str) {
        Automaton automaton;
        for (char c : str)
            automaton.get(c);
        return automaton.sign * automaton.ans;
    }
};
```

