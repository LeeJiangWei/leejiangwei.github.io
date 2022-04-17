---
title: 下一个排列
categories:
  - LeetCode
  - HOT 100
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-04-17 19:47:00
---

# 下一个排列

https://leetcode-cn.com/problems/next-permutation/

## 交换 + 反转

容易看出，下一个排列的数字在数学上一定会比前一个排列大。因此，我们希望：
+ 下一个数比当前数大，且是一个排列
   + 可以通过将一个靠后的「大数」和一个靠前的「小数」交换来得到
+ 下一个数增加的幅度尽可能小
   +「小数」尽可能地靠右：比如 `123465`，下一个排列应该把 5 和 4 交换而不是把 5 和 1 交换
   + 「大数」尽可能地小：比如 `123465`，下一个排列应该把 5 和 4 交换而不是把 6 和 4 交换
   + 交换后，需要将「大数」后面的数重排为升序，这样这个排列才就是交换后最小的

算法步骤：
1. 从后向前查找第一个相邻升序的元素对 (i,j)，满足 A[i] < A[j]。此时 [j,end) 必然是降序
2. 在 [j,end) 从后向前查找第一个满足 A[i] < A[k] 的 k。A[i]、A[k] 分别就是上文所说的「小数」、「大数」
3. 将 A[i] 与 A[k] 交换
4. 可以断定这时 [j,end) 必然是降序，逆置 [j,end)，使其升序
5. 如果在步骤 1 找不到符合的相邻元素对，说明当前 [begin,end) 为一个降序顺序，则直接跳到步骤 4

```go
func nextPermutation(nums []int)  {
    n := len(nums)
    if n <= 1 {
        return
    }

    i, j, k := n-2, n-1, n-1

    // find: nums[i] < nums[j]
    for i >= 0 && nums[i] >= nums[j] {
        i--
        j--
    }

    if i >= 0 {
        // find: nums[i] < nums[k]
        for nums[i] >= nums[k] {
            k--
        }

        // swap
        nums[i], nums[k] = nums[k], nums[i]
    }

    // reverse [j:end]
    for i, j := j, n-1; i < j; i, j = i+1, j-1 {
        nums[i], nums[j] = nums[j], nums[i]
    }
}
```
