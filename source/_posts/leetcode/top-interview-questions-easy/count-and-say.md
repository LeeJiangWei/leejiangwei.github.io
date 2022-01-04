---
title: 外观数列
categories:
  - LeetCode
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-01-04 21:01:35
---

# 外观数列

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnpvdm/

https://leetcode-cn.com/problems/count-and-say/

顺带一提，从今天开始用 Golang 刷题，以便掌握算法的同时熟悉语言特性。

## 递归

```go
func countAndSay(n int) string {
    if n == 1 {
        return "1"
    }

    str := countAndSay(n - 1)

    var sb strings.Builder
    
    for start, end := 0, 0; start < len(str); start = end {
        for end < len(str) && str[end] == str[start] {
            end++
        }
        sb.WriteString(strconv.Itoa(end - start))
        sb.WriteByte(str[start])
    }

    return sb.String()
}
```

## Note of Golang

+ 用 `strings.Builder` 来拼接字符串，因为如果使用 `+=` 运算符的话会在不停分配新的内存导致效率变低。
+ Golang 中变量可以平行赋值，先计算等式右边的值，然后从左到右依次赋值。
