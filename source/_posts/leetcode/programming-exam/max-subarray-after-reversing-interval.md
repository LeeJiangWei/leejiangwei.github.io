---
title: 美团笔试题（3.5） 编程题 2. 反转数组一段区间后最大子数组和
categories:
  - LeetCode
  - 笔试编程题
tags:
  - Go
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-03-16 21:41:37
---

# 美团笔试题（3.5） 编程题 2. 反转数组一段区间后最大子数组和

## 题目描述

给一个数组，可以任意反转数组的一段区间（也可以不反转），求最大子数组和。例：

输入：

```
-1, 3, -5, 2, -1, 3
```

输出：

```
7
```

解释：反转以元素 -5, 3 作为端点的区间，得到 `-1, 3, 3, -1, 2, -5`，最大子数组和为 7。 

## 解法：动态规划

**核心思想：如果反转了某段区间 `[i..j]` 能够得到一个和最大的子数组，那这个操作实际上使得 `[i..j]` 中最大子数组跟 `[0..i]` 中（或者是 `[j..n]` 中）最大子数组拼了起来，形成了一个更大的子数组。**

以输入示例为例，反转 -5 到 3 之间的元素得到的最大子数组，实际上是由 `[3]` 跟 `[2, -1, 3]` 这两个小一点的子数组拼接而成，但是它们由于有个 -5 隔在中间导致不能连续。进行反转操作后，-5 就到最后去了，因此形成了更大的连续子数组。

更一般地说，考虑一个切分点 i ，以切分点为界的数组两边各有一个最大子数组。遍历所有可能的 i ，求这两个子数组的和的**最大值**即可。以输入示例为例，-5 就是这个切分点，至于具体反转哪一段区间则不需要关心，只要知道反转操作一定能使两个子数组变成连续的。

于是可以用动态规划求从左到 i 的最大子数组，以及从右到 i 的最大子数组，最后遍历所有的 i 找到一个最大值即可。

注意这里的 DP 数组定义， `maxSum_left2i[i]` 表示从 0 到 i 这一段区间的最大子数组和（不一定要以 i 结尾） ，详见 [Kadane’s Algorithm](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/)。（如果 `dp[i]` 表示以 i 结尾的最大子数组和，则为了找出 `[0..i]` 中最大的值，还要再遍历一遍）

```go
func main() {
	arr := []int{-1, 3, -5, 2, -1, 3}
	n := len(arr)

	// 动态规划求 0 到 i 的最大子数组和
	maxSum_left2i := make([]int, n)
	if arr[0] > 0 {  // DP base case
		maxSum_left2i[0] = arr[0]
	}
	for i, maxSumEndWith_i := 1, 0; i < n; i++ {
		maxSumEndWith_i += arr[i]
		if maxSumEndWith_i < 0 {
			maxSumEndWith_i = 0
		}

		maxSum_left2i[i] = max(maxSum_left2i[i-1], maxSumEndWith_i)
	}

	// 动态规划求 n-1 到 i 的最大子数组和
	maxSum_i2right := make([]int, n)
	if arr[n-1] > 0 {  // DP base case
		maxSum_i2right[n-1] = arr[n-1]
	}
	for i, maxSumEndWith_i := n-2, 0; i >= 0; i-- {
		maxSumEndWith_i += arr[i]
		if maxSumEndWith_i < 0 {
			maxSumEndWith_i = 0
		}

		maxSum_i2right[i] = max(maxSum_i2right[i+1], maxSumEndWith_i)
	}

	// 遍历所有可能的 i 找出最大值
	res := 0
	for i := 0; i < n; i++ {
		res = max(res, maxSum_left2i[i] + maxSum_i2right[i])
	}

	fmt.Println(res)
}

func max(values ...int) int {
	res := values[0]
	for _, v := range values {
		if v > res {
			res = v
		}
	}
	return res
}
```
