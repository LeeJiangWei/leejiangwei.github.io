---
title: C++常用泛型算法
date: 2021-07-14 11:07:33
categories:
- 编程语言
- C++
tags:
- C++
index_img: /img/cpp.jpg
---

# C++常用泛型算法

https://www.geeksforgeeks.org/c-magicians-stl-algorithms/

https://en.cppreference.com/w/cpp/algorithm

```c++
#include <algorithm> // 泛型算法
#include <numeric>   // 数值泛型算法
```

## 只读算法

| 操作 | 函数                            | 返回                             |
| ---- | ------------------------------- | -------------------------------- |
| 查找 | find(v.begin(), v.end(), value) | 满足条件的迭代器，找不到则为尾后 |
|      |                                 |                                  |
|      |                                 |                                  |

## 写容器算法

| 操作     | 函数                     | 返回 |
| -------- | ------------------------ | ---- |
| 交换元素 | swap(v.begin(), v.end()) | void |
|          |                          |      |
|          |                          |      |

## 重排容器算法

| 操作               | 函数                       | 返回                             |
| ------------------ | -------------------------- | -------------------------------- |
| 排序               | sort(v.begin(), v.end())   | void                             |
| 将不重复的排在前面 | unique(v.begin(), v.end()) | 一个指向不重复值范围末尾的迭代器 |
|                    |                            |                                  |

