---
title: 最长公共前缀
categories:
  - LeetCode
  - 初级算法
  - 字符串
tags:
  - Go
  - 字符串
index_img: /img/code.jpg
sticky: -10
date: 2022-01-05 18:40:30
---

# 最长公共前缀

https://leetcode-cn.com/leetbook/read/top-interview-questions-easy/xnmav1/

https://leetcode-cn.com/problems/longest-common-prefix/solution/

## 横向扫描

遍历所有字符串，计算并更新公共前缀。

```go
func longestCommonPrefix(strs []string) (prefix string) {
    prefix = strs[0]
    for _, str := range strs {
        prefix = LCP(prefix, str)
        if prefix == "" {
            return 
        }
    }
    return
}

func LCP(str1 string, str2 string) string {
    index := 0
    
    for index < len(str1) && index < len(str2) && str1[index] == str2[index] {
        index++
    }

    return str1[:index]
}
```
+ 用字符切片来保存返回结果

## 纵向扫描

遍历字符串相同位置的字母，例如对于 flow flower florance，取第一个 flow 作为基准，先检查 f，然后检查fl，然后检查 flo。
以此类推，直到某个单词读完了或者有不通过检查的。

```go
func longestCommonPrefix(strs []string) string {
    var sb strings.Builder
    flag := true

    for i, r := range strs[0] {
        for _, rs := range strs {
            if i >= len(rs) || r != rune(rs[i]) {
                flag = false
                break
            }
        }
        if flag {
           sb.WriteRune(r)
        }
    }

    return sb.String()
}
```

我的解法：用 `strings.Builder` 来拼接字符串，其实没有必要，对于这种不需要重新组装字符串的情况，直接用切片就行了。


```go
func longestCommonPrefix(strs []string) (prefix string) {
    for i := 0; i < len(strs[0]); i++ {
        for j := 1; j < len(strs); j++ {
            if i == len(strs[j]) || strs[j][i] != strs[0][i] {
                return strs[0][:i]
            }
        }
    }
    return strs[0]
}
```