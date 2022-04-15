---
title: 最长回文子串
categories:
  - LeetCode
  - HOT 100
tags:
  - Go
  - 字符串
  - 动态规划
index_img: /img/code.jpg
sticky: -10
date: 2022-04-15 11:25:00
---

# 最长回文子串

https://leetcode-cn.com/problems/longest-palindromic-substring/

注意：这题是最长回文**子串**，而不是**子序列**。**子串**需要在原始字符串中是连续的。

## 动态规划

按照惯例，需要寻找状态转移。假设 `s[i+1..j-1]` 是回文，则 `s[i..j]` 是回文的充分必要条件是 `s[i] == s[j]`。换言之，如果 `s[i] == s[j]`，则 `s[i..j]` 是不是回文取决于 `s[i+1..j-1]` 是不是回文

另外有 2 个 base case：字符串长度为 1，则它自己是一个回文串；字符串长度为 2，且这两个字符相同。

定义 `dp[i][j]` 表示子串 `s[i..j]` 是否为回文。只有一个字母的 base case 在初始化数组时确定，而只有两个字母的 base case 则在循环中判断。

时间复杂度一样是 O(n^2)，空间复杂度也是 O(n^2)。

```go
func longestPalindrome(s string) string {
    n := len(s)

    dp := make([][]bool, n)
    for i := range dp {
        dp[i] = make([]bool, n)
    }

    for i := 0; i < n; i++ {
        dp[i][i] = true
    }

    maxLen := 1
    start := 0

    // 遍历所有可能的子串长度 l
    for l := 2; l <= n; l++ {
        // 遍历所有可能的开始位置 i
        for i := 0; i < n; i++ {
            j := l + i - 1
            if j >= n {
                break
            }
            if s[i] != s[j] {
                dp[i][j] = false
            } else {
                if j - i + 1 <= 3 {
                    // 如果子串长度为 2 或 3，则一定是回文串
                    dp[i][j] = true
                } else {
                    dp[i][j] = dp[i+1][j-1]
                }
            }

            // 更新最长子串
            if dp[i][j] && j - i + 1 > maxLen {
                maxLen = j - i + 1
                start = i
            }
        }
    }

    return s[start : start + maxLen]
}
```

## 从中心扩展

从边界条件开始向外扩展，遍历所有可能的边界条件（即只有 1 个或 2 个字母的情况），省去了 DP 数组的储存空间。

时间复杂度一样是 O(n^2)，空间复杂度 O(1)。

```go
func longestPalindrome(s string) string {
    if s == "" {
        return ""
    }
    start, end := 0, 0
    for i := 0; i < len(s); i++ {
        left1, right1 := expandAroundCenter(s, i, i)
        left2, right2 := expandAroundCenter(s, i, i + 1)
        if right1 - left1 > end - start {
            start, end = left1, right1
        }
        if right2 - left2 > end - start {
            start, end = left2, right2
        }
    }
    return s[start:end+1]
}

func expandAroundCenter(s string, left, right int) (int, int) {
    for ; left >= 0 && right < len(s) && s[left] == s[right]; left, right = left-1 , right+1 { }
    return left + 1, right - 1
}
```