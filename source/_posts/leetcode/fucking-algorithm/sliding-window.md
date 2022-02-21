---
title: 双指针：滑动窗口
categories:
  - LeetCode
  - Fucking
  - 数组
tags:
  - Go
  - 数组
  - 双指针
index_img: /img/code.jpg
sticky: -10
date: 2022-02-21 17:10:00
---

# 双指针：滑动窗口

滑动窗口框架：

```c++
/* 滑动窗口算法框架 */
void slidingWindow(string s, string t) {
    unordered_map<char, int> need, window;
    for (char c : t) need[c]++;
    
    int left = 0, right = 0;
    int valid = 0; 
    while (right < s.size()) {
        // c 是将移入窗口的字符
        char c = s[right];
        // 右移窗口
        right++;
        // 进行窗口内数据的一系列更新
        ...

        /*** debug 输出的位置 ***/
        printf("window: [%d, %d)\n", left, right);
        /********************/
        
        // 判断左侧窗口是否要收缩
        while (window needs shrink) {
            // d 是将移出窗口的字符
            char d = s[left];
            // 左移窗口
            left++;
            // 进行窗口内数据的一系列更新
            ...
        }
    }
}

```

## 最小覆盖子串

https://leetcode-cn.com/problems/minimum-window-substring/

右指针一直前进，知道找齐所有的字符；找齐字符后再收缩左指针，尽量取最短的子串。

```go
func minWindow(s string, t string) string {
    window := make(map[byte]int)  // 记录当前窗口内的字符情况
    need := make(map[byte]int)    // 记录需要的字符
    for i := 0; i < len(t); i++ {
        need[t[i]] += 1
    }

    left, right := 0, 0  // 左右指针
    valid := 0           // 记录已找齐字母数量

    start, minLen := 0, math.MaxInt32

    for right < len(s) {
        c := s[right]
        right++
        
        // 如果需求这个字符，就记录它，并且
        if val, ok := need[c]; ok {
            window[c]++
            // 判断它的数量是否已经足够
            if window[c] == val {
                valid++
            }
        }

        // 在所有字母都已经找到时开始收缩窗口
        for valid == len(need) {
            if right - left < minLen {
                minLen = right - left
                start = left
            }

            d := s[left]
            left++  // 窗口收缩

            // 每次收缩窗口兜要判断字符是否需求
            if val, ok := need[d]; ok {
                if window[d] == val {
                    valid--
                }
                window[d]--
            }
        }
    }

    if minLen == math.MaxInt32 {
        return ""
    } else {
        return s[start : start+minLen]
    }
}
```
## 字符串的排列

https://leetcode-cn.com/problems/permutation-in-string/

还是类似的思路

```go
func checkInclusion(s1 string, s2 string) bool {
    window, need := make(map[byte]int), make(map[byte]int)
    for i := 0; i < len(s1); i++ {
        need[s1[i]]++
    }

    left, right := 0, 0
    valid := 0
    
    for right < len(s2) {
        c := s2[right]
        right++

        // 每处理一个字符，都要检查这个字符是不是需求的
        if val, ok := need[c]; ok {
            window[c]++
            if window[c] == val {
                valid++
            }
        }
        
        // 左指针收缩条件：字符串太长
        for right - left >= len(s1) {
            if valid == len(need) {
                return true
            }
            d := s2[left]
            left++
            // 每处理一个字符，都要检查这个字符是不是需求的
            if val, ok := need[d]; ok {
                if window[d] == val {
                    valid--
                }
                window[d]--
            }
        }
    }
    return false
}
```

## 找到字符串中所有字母异位词

https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/

一样的题，不过是要输出字符串的开始位置。

```go
func findAnagrams(s string, p string) []int {
    window, need := make(map[byte]int), make(map[byte]int)
    for i := 0; i < len(p); i++ {
        need[p[i]]++
    }

    left, right := 0, 0
    valid := 0
    res := make([]int, 0)

    for right < len(s) {
        c := s[right]
        right++

        if val, ok := need[c]; ok {
            window[c]++
            if window[c] == val {
                valid++
            }
        }

        for right - left >= len(p) {
            if valid == len(need) {
                res = append(res, left)
            }

            d := s[left]
            left++

            if val, ok := need[d]; ok {
                if window[d] == val {
                    valid--
                }
                window[d]--
            }
        }
    }
    return res
}
```

## 无重复字符的最长子串

https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/

这题还要更简单一些，无重复字串直接查看窗口内的哈希表就好了，一旦右指针前进使得有字符重复就收缩左指针，直到没有重复位置，再计算新的长度。

```go
func lengthOfLongestSubstring(s string) int {
    window := make(map[byte]int)

    left, right := 0, 0
    maxLen := 0
    for right < len(s) {
        c := s[right]
        right++
        window[c]++
        
        for window[c] > 1 {
            d := s[left]
            left++
            window[d]--
        }
        if right - left > maxLen {
            maxLen = right - left
        }
    }
    return maxLen
}
```
