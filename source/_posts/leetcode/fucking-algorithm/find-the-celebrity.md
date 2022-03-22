---
title: 寻找名人
categories:
  - LeetCode
  - Fucking
  - 图
tags:
  - Go
  - 图
index_img: /img/code.jpg
sticky: -10
date: 2022-03-22 15:19:00
---

# 寻找名人

https://labuladong.gitee.io/algo/2/20/44/

寻找名人问题：在 n 人中有一个人是名人，其他人都认识名人，而名人不认识任何人。题目提供了 `knows(a, b)` 这样一个函数来判断一个人是否认识另一个。

这个题有个有趣的性质：观察两人是否认识，至少可以排除一个人一定不是名人。设一个人为名人候选 `candidate`，另一个人记为 `other` 。两个人之间的关系只可能有 4 种：

+ `candidate` 认识 `other`：因为名人不认识其他人，所以 `candidate` 肯定不是名人，排除 `candidate`。
+ `other` 认识 `candidate`：同理，排除 `other`。
+ 两个人互相认识，则他俩都不是名人，因为名人不认识其他人。
+ 两个人互不认识，则他俩也都不是名人，因为所有人都认识名人。

开始时，将所有人放进队列，然后每次取出 2 个人观察他们的关系，根据上面的关系排除掉一个人（如果是两个人都不是名人的情况，随便排除一个，方便写代码），将另一人归回队列中，直到只剩下一个人。然后再遍历一次其他人来检查这个人是否是名人。

```go
func findCelebrity(n int) int {
	if n == 1 {
		return 0
	}

	queue := make([]int, 0)
	for i := 0; i < n; i++ {
		queue = append(queue, i)
	}

	for len(queue) >= 2 {
		candidate := queue[0]
		other := queue[1]
		queue = queue[2:]

		if knows(candidate, other) || !knows(other, candidate) {
			queue = append(queue, other)  // 排除 candidate
		} else {
			queue = append(queue, candidate)  // 排除 other
		}
	}

	candidate := queue[0]
	for other := 0; other < n; other++ {
		if other == candidate {
			continue
		}
		if !knows(other, candidate) || knows(candidate, other) {
			return -1
		}
	}

	return candidate
}

// 模拟 leetcode API，此处名人是 2
func knows(a, b int) bool {
	adjacentMatrix := [][]int{
		{1, 1, 1, 0},
		{1, 1, 1, 1},
		{0, 0, 1, 0},
		{0, 0, 1, 1},
	}
	return adjacentMatrix[a][b] == 1
}
```

也可以再简化，把队列去掉，每次只维护一个候选人变量即可，排除掉一个人时就让另一个成为候选。