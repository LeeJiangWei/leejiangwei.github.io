---
title: 颜色分类
categories:
  - LeetCode
  - HOT 100
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-07-18 20:45:00
---

# 颜色分类

https://leetcode.cn/problems/sort-colors/

## 双指针

用两个指针 p0, p1 分别指示**下一个**可以交换的 0 的位置和 1 的位置。（即当前 p0 p1 位置上不是 0 或 1）。

+ 交换 p1 时，简单地交换 p1 和当前位置 i 并且 p1++ 即可
+ 交换 p0 时，由于 0 的后面可能还会有 1，交换 p0 和 i 后需要同时 p0++ 和 p1++。另外如果 p0 < p1，表明 0 后面确实有 1，这时候刚才是把 1 给换到 i 去了，因此 i 再和 p1 交换一次并 p1++

```go
func sortColors(nums []int)  {
    var p0, p1 int
    for i := 0; i < len(nums); i++ {
        if nums[i] == 0 {
            nums[i], nums[p0] = nums[p0], nums[i]
            if p0 < p1 {
                nums[i], nums[p1] = nums[p1], nums[i]
            }
            p0++
            p1++
        } else if nums[i] == 1 {
            nums[i], nums[p1] = nums[p1], nums[i]
            p1++
        }
    }
}
```