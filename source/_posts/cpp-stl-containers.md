---
title: C++常用标准库容器
date: 2021-07-14 13:44:23
categories:
- 编程语言
- C++
tags:
- C++
index_img: /img/cpp.jpg
---

# C++常用标准库容器

https://www.geeksforgeeks.org/containers-cpp-stl/

## 顺序容器 (sequence containers)

### vector

可以动态扩展的列表

#### 创建

```c++
using namespace std;

vector<int> v;
```

#### 常用操作

| 操作                           | 函数                                                |
| ------------------------------ | --------------------------------------------------- |
| 插入                           | v.insert(v.begin(), 3)                              |
| 插入到最后面<br />插入到最前面 | v.push_back(3)<br />v.push_front(3)                 |
| 删除                           | v.erase(v.begin())<br />v.erase(v.begin(), v.end()) |
| 删除尾元素<br />删除头元素     | v.pop_back()<br />v.pop_front()                     |
| 计数                           | v.count(3)                                          |

## pair类型

关联容器可能会使用到的数据类型，定义在头文件`utility`中。具有两个数据成员，名字为`first`以及`second`。

## 关联容器 (associative containers)



### map

#### 创建

```c++
using namespace std;

map<string, int> map;
```

#### 常用操作

| 操作 | 函数                                                         | 返回                                                         |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 插入 | map.insert({"word", 1})<br />map.insert(make_pair("word", 1))<br />map.insert(pair<string, size_t>("word", 1)) |                                                              |
| 删除 | map.erase("word")<br />map.erase(iterator)                   |                                                              |
| 查找 | map.find("word")                                             | 返回指向第一个找到元素的迭代器<br />迭代器指向的类型为pair<br />若未找到，则返回尾后迭代器 |
| 计数 | map.count("word")                                            | 计数值                                                       |

#### 下标操作

map（以及unordered_map）可以使用下标运算符，获取关键字对应的值。

如果关键字不存在，会用这个关键字创建一个新的元素，并对关联的值初始化。因此不应该直接用下标操作来进行查找，应该使用`find()`。

```c++
map["hello"] = 1;  // 会插入一个关键字为hello的元素，且值为1
```

## 无序关联容器 (unordered associative containers)

使用哈希函数组织的关联容器，查找速度更快，时间复杂度平均`O(1)`，最坏`O(n)`。在关键字类型元素没有明显有序关系时，可以使用无序容器。

### unordered_set

#### 创建

### unordered_map

无序键值对

#### 创建

```c++
using namespace std;

unordered_map<string, int> umap;
```

#### 常用操作

| 操作 | 函数                                                         | 返回 |
| ---- | ------------------------------------------------------------ | ---- |
| 插入 | umap.insert({"word", 1})<br />umap.insert(make_pair("word", 1))<br />umap.insert(pair<string, size_t>("word", 1)) |      |
| 查询 | umap.find("word")                                            |      |
| 计数 | umap.count("word")                                           |      |

## 容器适配器 (container adapters)

