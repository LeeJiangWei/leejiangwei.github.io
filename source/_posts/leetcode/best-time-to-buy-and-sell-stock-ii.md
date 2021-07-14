---
title: 买卖股票的最佳时机 II
date: 2021-06-16 20:26:05
categories:
- LeetCode
- 初级算法
tags:
- 数组
- 贪心算法
- C++
index_img: /img/code.jpg
---
# 买卖股票的最佳时机 II
https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x2zsx1/
https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/

## 题解

### 贪心法
只看相邻的两天，只要后一天比前一天高，就是收益，非常简单。
```c++
int maxProfit(vector<int>& prices) {
    int result = 0;
    for (auto curr = prices.begin() + 1; curr != prices.end(); ++curr) {
        if (*curr > *(curr - 1))
            result += *curr - *(curr - 1);
    }
    return result;
}
```
### 我的解法
刚做完一个双指针，指魔怔了，嗯是搞了个双指针。其实就是找到每一段递增区间的起点和终点。
```c++
int maxProfit(vector<int>& prices) {
    if (prices.size() <= 1)
        return 0;
    
    auto min = prices.begin();
    auto max = min;
    auto end = prices.end();
    int result = 0;

    while (min != end && max != end) {
        while ((min+1) != end && *min > *(min + 1))
            ++min;
        max = min;
        while ((max+1) != end && *max < *(max + 1))
            ++max;
        result += *(max) - *(min);
        if (min != end && max != end) {
            ++max;
            min = max;
        } else {
            break;
        }
    }

    return result;
}
```