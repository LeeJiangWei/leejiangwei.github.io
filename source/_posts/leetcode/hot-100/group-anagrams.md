---
title: 字母异位词分组
categories:
  - LeetCode
  - HOT 100
tags:
  - Go
  - 字符串
index_img: /img/code.jpg
sticky: -10
date: 2022-07-16 17:31:00
---

# 字母异位词分组

https://leetcode.cn/problems/group-anagrams/

## 哈希表

### 排序字符串作为 key

排序单词，异位词的排序结果相同。

排序长度为 k 的单词会用去 klogk 的复杂度，对 n 个单词排序，则总时间复杂度为 O(nklogk)，空间复杂度为哈希表储存所有字符串的 O(nk)。

```go
func groupAnagrams(strs []string) [][]string {
    groups := make(map[string][]string)

    for _, word := range strs {
        chars := []byte(word)
        sort.Slice(chars, func(i, j int) bool {
            return chars[i] < chars[j]
        })
        key := string(chars)

        groups[key] = append(groups[key], word)
    }

    var results [][]string
    for _, words := range groups {
        var sames []string
        for _, word := range words {
            sames = append(sames, word)
        }
        results = append(results, sames)
    }
    return results
}
```

### 统计字母出现次数作为 key

实际上直接统计单词字母出现速度会更快，因为不需要排序，时间复杂度为 O(nk)，空间复杂度为 O(n(k+|V|))

```go
func groupAnagrams(strs []string) [][]string {
    groups := make(map[[26]int][]string)

    for _, word := range strs {
        var key [26]int
        for _, char := range word {
            key[int(char - 'a')] += 1
        }

        groups[key] = append(groups[key], word)
    }

    var results [][]string
    for _, words := range groups {
        var sames []string
        for _, word := range words {
            sames = append(sames, word)
        }
        results = append(results, sames)
    }
    return results
}
```
