---
categories:
  - LeetCode
  - 初级算法
tags:
  - C++
  - 数组
  - 双指针
  - 哈希表
index_img: /img/code.jpg
date: 2021-07-14 11:03:59
title: 两个数组的交集 II
---

# 两个数组的交集 II

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/x2y0c2/

https://leetcode-cn.com/problems/intersection-of-two-arrays-ii/

## 双指针

将数组排序后，使用双指针，值相同则加入并集，否则向后移动**值比较小**的那个指针，直到其中一方越界。

时间复杂度：排序为`O(nlogn + mlogm)`，双指针移动为`O(n + m)`，加起来就是`O(nlogn + mlogm)`

空间复杂度：由于`vector`的大小是动态改变的，复杂度为`O(1)`

```c++
vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
    sort(nums1.begin(), nums1.end());
    sort(nums2.begin(), nums2.end());

    vector<int> intersection;

    int n1 = 0, n2 = 0;

    while ( n1 != nums1.size() && n2 != nums2.size() ) {
        if (nums1[n1] == nums2[n2]) {
            intersection.push_back(nums1[n1]);
            ++n1;
            ++n2;
        } else if (nums1[n1] > nums2[n2]) {
            ++n2;
        } else {
            ++n1;
        }
    }

    return intersection;
}
```

## 哈希表

使用哈希表来记录数字以及出现的次数。

1. 遍历较短的数组，依次将数字插入哈希表。
2. 遍历较长的数组，查询哈希表中是否已有该元素。如果有，就将它加入并集中，并将哈希表中的计数减一；如果没有则无需采取任何操作。

时间复杂度：遍历两个数组所用的时间`O(m + n)`，哈希表的插入和查询平均都是`O(1)`。

空间复杂度：创建了大小为较短数组长度的哈希表，因此是`O(min(m, n))`。

```c++
vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
    if (nums1.size() < nums2.size()) return intersect(nums2, nums1);

    unordered_map<int, int> m;
    vector<int> intersection;

    for (int x: nums1) {
        ++m[x];
    }

    for (int x: nums2) {
        if (m.find(x) != m.end() && (m.find(x) -> second) > 0) {
            --m[x];
            intersection.push_back(x);
        }
    }

    return intersection;
}
```

实现时，可以不用`find(x)`来查找，直接利用`count(x)`来判断表中是否存该数字，计数减一后判断计数值是不是变成0了，是的话就从表中删去此元素。
