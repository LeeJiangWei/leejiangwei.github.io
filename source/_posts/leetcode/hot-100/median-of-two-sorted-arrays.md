---
title: 寻找两个正序数组的中位数
categories:
  - LeetCode
  - HOT 100
tags:
  - Go
  - 数组
index_img: /img/code.jpg
sticky: -10
date: 2022-04-15 11:40:00
---

# 寻找两个正序数组的中位数

https://leetcode-cn.com/problems/median-of-two-sorted-arrays/

最简单的方法是合并数组，需要 O(m+n) 的复杂度。

## 二分查找

利用二分查找，可以将复杂度降为 O(log(m+n))。

https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xun-zhao-liang-ge-you-xu-shu-zu-de-zhong-wei-s-114/

根据中位数定义，假设将数组合并了，则：
+ `m + n` 为奇数时，中位数在数组中的位置为 `(m + n)/2`
+ `m + n` 为偶数时，中位数是位置为 `(m + n)/2` 和 `(m + n)/2 + 1` 的两个数的平均数

这个位置实际上表明了有多少个元素比中位数小。利用这一点，我们不用合并数组，只需要在两个数组中找出第 `k` 小的元素即可，其中 `k = (m+n)/2` 或 `k = (m+n)/2 + 1`。（如果 `m + n`是奇数，就只用找 `k = (m+n)/2` 的情况，如果是偶数，则两个都要找，然后求平均）

所以只要实现在两个数组中找到第 k 小的元素，这道题就可以解决了。

每次检查 A B 两个数组的 `k/2 - 1` 这个位置。如果 `A[k/2 -1] < B[k/2 - 1]`，则因为数组有序，比 `A[k/2 - 1]` 这个数还小的数最多只有 A 中它前面的 `k/2 - 1` 个数以及 B 前面的最多 `k/2 - 1` 个数，最多也只有 `k-2` 个数比它小，那它肯定不是中位数，这个数以及它前面的全部排除。

假设这一步排除掉了 `x` 个数，且在数组中把这些数删掉。则新的问题变为在剩下的数组中寻找第 `k - x` 小的数了，是一模一样的子问题。更新 `k` 值，然后利用上面的逻辑一直迭代下去，直到最后 `k == 1`，就只用比较两个剩余数组最小的元素哪个更小就好了。有可能会出现将其中一个数组全部排除的情况，此时直接返回另一个数组第 `k` 个数即可。需要注意 `k` 的更新必须减去实际上排除掉的数字数目。

```go
func findMedianSortedArrays(nums1 []int, nums2 []int) float64 {
    totalLength := len(nums1) + len(nums2)
    if totalLength%2 == 1 {
        midIndex := totalLength/2
        return float64(getKthElement(nums1, nums2, midIndex + 1))
    } else {
        midIndex1, midIndex2 := totalLength/2 - 1, totalLength/2
        return float64(getKthElement(nums1, nums2, midIndex1 + 1) + getKthElement(nums1, nums2, midIndex2 + 1)) / 2.0
    }
    return 0
}

func getKthElement(nums1, nums2 []int, k int) int {
    // 记录两个数组的起始点，在它们之前的数已经被排除
    index1, index2 := 0, 0
    for {
        // 数组 1 已经全部排除
        if index1 == len(nums1) {
            return nums2[index2 + k - 1]
        }
        // 数组 2 已经全部排除
        if index2 == len(nums2) {
            return nums1[index1 + k - 1]
        }
        if k == 1 {
            return min(nums1[index1], nums2[index2])
        }

        // 检查 k/2 - 1 这个位置，注意越界
        half := k/2
        newIndex1 := min(index1 + half, len(nums1)) - 1
        newIndex2 := min(index2 + half, len(nums2)) - 1

        pivot1, pivot2 := nums1[newIndex1], nums2[newIndex2]
        if pivot1 <= pivot2 {
            // 排除数组 1 中的数，更新 k 以及数组起始点
            k -= (newIndex1 - index1 + 1)
            index1 = newIndex1 + 1
        } else {
            // 排除数组 1 中的数，更新 k 以及数组起始点
            k -= (newIndex2 - index2 + 1)
            index2 = newIndex2 + 1
        }
    }
    return 0
}

func min(x, y int) int {
    if x < y {
        return x
    }
    return y
}
```

