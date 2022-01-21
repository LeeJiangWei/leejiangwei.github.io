---
title: 合并两个有序数组
categories:
  - LeetCode
  - 初级算法
  - 排序和搜索
tags:
  - Go
  - 双指针
index_img: /img/code.jpg
sticky: -10
date: 2022-01-21 22:09:44
---

# 合并两个有序数组

## 双指针

由于数组 `nums1` 的后面留够了足够的空位（`nums2` 元素的个数），如果从 `nums1` 的后面开始放入元素，可以保证 `nums1` 的元素在取出前不会被覆盖。

因此使用双指针，从后面开始填充  `nums1`。

```go
func merge(nums1 []int, m int, nums2 []int, n int)  {
    pm := m - 1
    pn := n - 1
    curr := len(nums1) - 1

    for pm != -1 && pn != -1 {
        if nums1[pm] > nums2[pn] {
            nums1[curr] = nums1[pm]
            pm--
        } else {
            nums1[curr] = nums2[pn]
            pn--
        }
        curr--
    }
    
    // 其中一个指针到达尽头后，将未到达尽头的那个数组填充完
    for pm != -1 {
        nums1[curr] = nums1[pm]
        curr--
        pm--
    }
    for pn != -1 {
        nums1[curr] = nums2[pn]
        curr--
        pn--
    }
}
```