---
title: 前缀树的实现和应用
categories:
  - LeetCode
  - Fucking
  - 数据结构设计
tags:
  - Go
index_img: /img/code.jpg
sticky: -10
date: 2022-02-23 20:20:00
---

# 前缀树的实现和应用

https://labuladong.gitee.io/algo/2/20/47/

## 实现前缀树

### 基础数据结构

节点可以储存任意值，而节点所代表的字符是由父节点的 `Children` 数组索引确定的。

这个索引直接对应字符的 ASCII 码，例如 `int('A') -> 65`。

节点储存了值，才认为根节点到这个节点路径所代表的单词被储存了。

![橙色节点为储存了值的节点](https://labuladong.gitee.io/algo/images/trie/9.jpeg)

```go
const R = 256

type TrieNode struct {
	Val      interface{}
	Children [R]*TrieNode
}

type TrieMap struct {
	Size int
	root *TrieNode
}
```

### 前缀树的基本搜索

根据字符串 `key` 中的每一个字符作为子节点索引，在前缀树走下去。

+ 如果字符串没遍历完就无法继续搜索了，显然这个字符串不存在。
+ 如果字符串遍历完了，但对应节点没储存值，说明这个字符串也不存在。（它是其他更长前缀的一部分）

```go
// GetNode 从节点 node 开始搜索 key，如果存在返回对应节点，否则返回 nil
func (t *TrieMap) GetNode(node *TrieNode, key string) *TrieNode {
	p := node
	for i := 0; i < len(key); i++ {
        // 无法继续向下搜索
		if p == nil {
			return nil
		}
		c := key[i]
		p = p.Children[int(c)]
	}
	return p
}

// Get 搜索 key 对应的值，不存在则返回 nil
func (t *TrieMap) Get(key string) interface{} {
	x := t.GetNode(t.root, key)

	// x 为空或 x 的 val 字段为空都说明 key 没有对应的值
	if x == nil || x.Val == nil {
		return nil
	}
	return x.Val
}

// ContainsKey 判断 key 是否存在在 Map 中
func (t *TrieMap) ContainsKey(key string) bool {
	return t.Get(key) != nil
}
```

### 是否存在以 `prefix` 开头的键

同理，在前缀树中搜索这个字符串，只要能遍历完它，就说明存在以这个字符串为前缀的单词。

```go
// HasKeyWithPrefix 判断是否存在前缀为 prefix 的键
func (t *TrieMap) HasKeyWithPrefix(prefix string) bool {
	return t.GetNode(t.root, prefix) != nil
}
```

### 搜索某个单词在 TrieMap 中储存的前缀

只有当搜索到的节点储存了值，它才是一个储存了的前缀。

+ 找最短前缀，见到一个储存了值的节点就直接返回
+ 找最长前缀，记录最长前缀长度，搜索完前缀树再返回

```go
// ShortestPrefixOf 在所有键中寻找 query 的最短前缀
func (t *TrieMap) ShortestPrefixOf(query string) string {
	p := t.root
	for i := 0; i < len(query); i++ {
		// 无法向下搜索
		if p == nil {
			return ""
		}

		// 找到一个键是 query 的前缀就直接返回
		if p.Val != nil {
			return query[:i]
		}

		// 继续向下搜索
		c := query[i]
		p = p.Children[int(c)]
	}

	// query 本身就是一个键
	if p != nil && p.Val != nil {
		return query
	}

	return ""
}

// LongestPrefixOf 在所有键中寻找 query 的最长前缀
func (t *TrieMap) LongestPrefixOf(query string) string {
	p := t.root
	maxLen := 0

	for i := 0; i < len(query); i++ {
		// 无法向下搜索
		if p == nil {
			break
		}

		// 找到一个键是 query 的前缀，更新前缀的最大长度
		if p.Val != nil {
			maxLen = i
		}

		// 向下搜索
		c := query[i]
		p = p.Children[int(c)]
	}

	// query 本身就是一个键
	if p != nil && p.Val != nil {
		return query
	}

	return query[0:maxLen]
}
```

### 搜索具有某个 `prefix` 的所有键

实际上就是先定位到 `prefix` 的节点，以它为根节点，找出所有储存了值的子节点。

搜索时，使用回溯法搜索所有可能的子节点。

```go
// KeysWithPrefix 搜索前缀为 prefix 的所有键
func (t *TrieMap) KeysWithPrefix(prefix string) []string {
	res := make([]string, 0)
    // 先找到 prefix 所在的节点
	x := t.GetNode(t.root, prefix)
	if x == nil {
		return res
	}
    // 遍历搜索 prefix 的所有子节点，找出所有存在的键
	t.prefixTraverse(x, prefix, &res)
	return res
}

// 遍历以 node 节点为根的 Trie 树，找到所有键
func (t *TrieMap) prefixTraverse(node *TrieNode, path string, res *[]string) {
	if node == nil {
		return
	}

    // 搜索到存了值的节点就是一个键
	if node.Val != nil {
		*res = append(*res, path)
	}

	for c := 0; c < R; c++ {
		// 做选择
		path += string(c)
		t.prefixTraverse(node.Children[c], path, res)
		// 撤销选择
		path = path[:len(path)-1]
	}
}
```

### 搜索具有某个 `pattern` 的所有键

这里只匹配通配符 `.`，类似于基础搜索，但是如果遇到了通配符，要搜索当前节点的**所有子节点**。

```go
// KeysWithPattern 搜索模式为 pattern 的所有键
func (t *TrieMap) KeysWithPattern(pattern string) []string {
	res := make([]string, 0)
	t.patternTraverse(t.root, "", pattern, 0, &res)
	return res
}

// 遍历函数，尝试在「以 node 为根的 Trie 树中」匹配 pattern[i..]
func (t *TrieMap) patternTraverse(node *TrieNode, path string, pattern string, i int, res *[]string) {
	// 匹配失败
	if node == nil {
		return
	}

	// pattern 匹配完成
	if i == len(pattern) {
		if node.Val != nil {
			*res = append(*res, path)
		}
		return
	}

	c := pattern[i]
	if c == '.' {
		// 用回溯法匹配任意字符（搜索所有子节点）
		for j := 0; j < R; j++ {
			path += string(j)
			t.patternTraverse(node.Children[j], path, pattern, i+1, res)
			path = path[:len(path)-1]
		}
	} else {
		path += string(c)
		t.patternTraverse(node.Children[int(c)], path, pattern, i+1, res)
		path = path[:len(path)-1]
	}
}
```

### 模式 `pattern` 是否存在

类似基本搜索，加入了对通配符的特殊判断。

```go
// HasKeyWithPattern 判断是和否存在模式为 pattern 的键
func (t *TrieMap) HasKeyWithPattern(pattern string) bool {
	return t.hasKeyWithPattern(t.root, pattern, 0)
}

// 从 node 节点开始匹配 pattern[i..]，返回是否成功匹配
func (t *TrieMap) hasKeyWithPattern(node *TrieNode, pattern string, i int) bool {
	if node == nil {
		return false
	}
	if i == len(pattern) {
		return node.Val != nil
	}

	c := pattern[i]

	// 不是通配符，则从对应的子节点开始匹配
	if c != '.' {
		return t.hasKeyWithPattern(node.Children[int(c)], pattern, i+1)
	}
	// 是通配符，尝试匹配所有字符
	for j := 0; j < R; j++ {
		if t.hasKeyWithPattern(node.Children[j], pattern, i+1) {
			return true
		}
	}

	return false
}
```

### 插入元素

按照给定的 `key` 的路径遍历前缀树，遇到空节点就新建一个，直到遍历完 `key`，将值储存在最后的节点中。

```go
// Put 在 map 中添加或修改键值对
func (t *TrieMap) Put(key string, val interface{}) {
	if !t.ContainsKey(key) {
		t.Size++
	}
	t.root = t.put(t.root, key, val, 0)
}

// 向以 node 为根的 Trie 树中插入 key[i..]，返回插入完成后的根节点
func (t *TrieMap) put(node *TrieNode, key string, val interface{}, i int) *TrieNode {
	// 如果树枝不存在，新建
	if node == nil {
		node = new(TrieNode)
	}
	// key 的路径已插入完成，将值 val 存入节点
	if i == len(key) {
		node.Val = val
		return node
	}

	c := key[i]
	// 递归插入子节点，并接收返回值
	node.Children[int(c)] = t.put(node.Children[int(c)], key, val, i+1)
	return node
}
```

### 删除元素

也是先遍历到 `key` 所在的节点，将这个节点储存的值设空，然后在后序位置清理递归路径上的孤儿节点。

```go
// Remove 在 Map 中删除 key
func (t *TrieMap) Remove(key string) {
	if !t.ContainsKey(key) {
		return
	}
	t.root = t.remove(t.root, key, 0)
	t.Size--
}

// 在以 node 为根的 Trie 树中删除 key[i..]，返回删除后的根节点
func (t *TrieMap) remove(node *TrieNode, key string, i int) *TrieNode {
	if node == nil {
		return nil
	}
	// 找到了 key 对应的 TrieNode，删除 val
	if i == len(key) {
		node.Val = nil
	} else {
		c := key[i]
		node.Children[int(c)] = t.remove(node.Children[int(c)], key, i+1)
	}

    // 在后序位置上清理递归路径上的节点

    // 如果节点储存了值（说明此节点代表了别的单词），则不需要删除
	if node.Val != nil {
		return node
	}
    // 只要存在子节点，也不需要被删除
	for c := 0; c < R; c++ {
		if node.Children[c] != nil {
			return node
		}
	}

    // 既没有储存值，也没有子节点，那就可以删除这个节点了
	return nil
}
```

## LeetCode：实现 Trie (前缀树)

https://leetcode-cn.com/problems/implement-trie-prefix-tree/

直接复制粘贴，嵌套一层即可。

这里不需要用到 `TrieMap` 节点中储存的值，只要它不是空的就行了，设为 true。

```go
type Trie struct {
	trieMap *TrieMap
}


func Constructor() Trie {
    return Trie{trieMap: &TrieMap{
		Size: 0,
		root: nil,
	}}
}


func (this *Trie) Insert(word string)  {
	this.trieMap.Put(word, true)  // 节点可以储存任意值
}


func (this *Trie) Search(word string) bool {
	return this.trieMap.ContainsKey(word)
}


func (this *Trie) StartsWith(prefix string) bool {
	return this.trieMap.HasKeyWithPrefix(prefix)
}

type TrieMap struct {
	/* 见上文 */
}
```

## LeetCode：单词替换

https://leetcode-cn.com/problems/replace-words/

将所有词根放入前缀树中，然后对于句子中所有的单词都查找它的最短前缀，替换回去即可。

```go
func replaceWords(dictionary []string, sentence string) string {
    m := TrieMap{0, nil}
    // 将词根放入前缀树中
    for _, v := range dictionary {
        m.Put(v, true)
    }

    words := strings.Fields(sentence)
    sb := &strings.Builder{}
    // 查找每个单词的最短前缀
    for i := 0; i < len(words); i++ {
        prefix := m.ShortestPrefixOf(words[i])
        if prefix == "" {
            sb.WriteString(words[i])
        } else {
            sb.WriteString(prefix)
        }
        
        // 单词之间加上空格
        if i != len(words) - 1 {
            sb.WriteString(" ")
        }
    }

    return sb.String()
}
```

## LeetCode：添加与搜索单词 - 数据结构设计

https://leetcode-cn.com/problems/design-add-and-search-words-data-structure/

这题就是我们已经实现好的功能，嵌套一层即可。注：直接套用代码会超时。

```go
type WordDictionary struct {
    trieMap *TrieMap
}


func Constructor() WordDictionary {
    return WordDictionary{trieMap: &TrieMap{
		Size: 0,
		root: nil,
	}}
}


func (this *WordDictionary) AddWord(word string)  {
    this.trieMap.Put(word, true)
}


func (this *WordDictionary) Search(word string) bool {
    return this.trieMap.HasKeyWithPattern(word)
}
```

## LeetCode：键值映射

https://leetcode-cn.com/problems/map-sum-pairs/

这题用到了节点储存的值，类型是 int，因此在取出来的时候记得使用类型断言。

```go
type MapSum struct {
    trieMap *TrieMap
}


func Constructor() MapSum {
    return MapSum{trieMap: &TrieMap{
		Size: 0,
		root: nil,
	}}
}


func (this *MapSum) Insert(key string, val int)  {
    this.trieMap.Put(key, val)
}


func (this *MapSum) Sum(prefix string) int {
    keys := this.trieMap.KeysWithPrefix(prefix)
    res := 0
    for _, k := range keys {
        res += this.trieMap.Get(k).(int)
    }
    return res
}
```

## 完整代码

```go
const R = 256

type TrieNode struct {
	Val      interface{}
	Children [R]*TrieNode
}

type TrieMap struct {
	Size int
	root *TrieNode
}

// GetNode 从节点 node 开始搜索 key，如果存在返回对应节点，否则返回 nil
func (t *TrieMap) GetNode(node *TrieNode, key string) *TrieNode {
	p := node
	for i := 0; i < len(key); i++ {
		if p == nil {
			// 无法继续向下搜索
			return nil
		}
		c := key[i]
		p = p.Children[int(c)]
	}
	return p
}

// Get 搜索 key 对应的值，不存在则返回 null
func (t *TrieMap) Get(key string) interface{} {
	x := t.GetNode(t.root, key)

	// x 为空或 x 的 val 字段为空都说明 key 没有对应的值
	if x == nil || x.Val == nil {
		return nil
	}
	return x.Val
}

// ContainsKey 判断 key 是否存在在 Map 中
func (t *TrieMap) ContainsKey(key string) bool {
	return t.Get(key) != nil
}

// HasKeyWithPrefix 判断是否存在前缀为 prefix 的键
func (t *TrieMap) HasKeyWithPrefix(prefix string) bool {
	return t.GetNode(t.root, prefix) != nil
}

// ShortestPrefixOf 在所有键中寻找 query 的最短前缀
func (t *TrieMap) ShortestPrefixOf(query string) string {
	p := t.root
	for i := 0; i < len(query); i++ {
		// 无法向下搜索
		if p == nil {
			return ""
		}

		// 找到一个键是 query 的前缀
		if p.Val != nil {
			return query[:i]
		}

		// 继续向下搜索
		c := query[i]
		p = p.Children[int(c)]
	}

	// query 本身就是一个键
	if p != nil && p.Val != nil {
		return query
	}

	return ""
}

// LongestPrefixOf 在所有键中寻找 query 的最长前缀
func (t *TrieMap) LongestPrefixOf(query string) string {
	p := t.root
	maxLen := 0

	for i := 0; i < len(query); i++ {
		// 无法向下搜索
		if p == nil {
			break
		}

		// 找到一个键是 query 的前缀，更新前缀的最大长度
		if p.Val != nil {
			maxLen = i
		}

		// 向下搜索
		c := query[i]
		p = p.Children[int(c)]
	}

	// query 本身就是一个键
	if p != nil && p.Val != nil {
		return query
	}

	return query[0:maxLen]
}

// KeysWithPrefix 搜索前缀为 prefix 的所有键
func (t *TrieMap) KeysWithPrefix(prefix string) []string {
	res := make([]string, 0)
	x := t.GetNode(t.root, prefix)
	if x == nil {
		return res
	}
	t.prefixTraverse(x, prefix, &res)
	return res
}

// 遍历以 node 节点为根的 Trie 树，找到所有键
func (t *TrieMap) prefixTraverse(node *TrieNode, path string, res *[]string) {
	if node == nil {
		return
	}

	if node.Val != nil {
		*res = append(*res, path)
	}

	for c := 0; c < R; c++ {
		// 做选择
		path += string(c)
		t.prefixTraverse(node.Children[c], path, res)
		// 撤销选择
		path = path[:len(path)-1]
	}
}

// KeysWithPattern 搜索模式为 pattern 的所有键
func (t *TrieMap) KeysWithPattern(pattern string) []string {
	res := make([]string, 0)
	t.patternTraverse(t.root, "", pattern, 0, &res)
	return res
}

// 遍历函数，尝试在「以 node 为根的 Trie 树中」匹配 pattern[i..]
func (t *TrieMap) patternTraverse(node *TrieNode, path string, pattern string, i int, res *[]string) {
	// 匹配失败
	if node == nil {
		return
	}

	// pattern 匹配完成
	if i == len(pattern) {
		if node.Val != nil {
			*res = append(*res, path)
		}
		return
	}

	c := pattern[i]
	if c == '.' {
		// 用回溯法匹配任意字符
		for j := 0; j < R; j++ {
			path += string(j)
			t.patternTraverse(node.Children[j], path, pattern, i+1, res)
			path = path[:len(path)-1]
		}
	} else {
		path += string(c)
		t.patternTraverse(node.Children[int(c)], path, pattern, i+1, res)
		path = path[:len(path)-1]
	}
}

// HasKeyWithPattern 判断是和否存在模式为 pattern 的键
func (t *TrieMap) HasKeyWithPattern(pattern string) bool {
	return t.hasKeyWithPattern(t.root, pattern, 0)
}

// 从 node 节点开始匹配 pattern[i..]，返回是否成功匹配
func (t *TrieMap) hasKeyWithPattern(node *TrieNode, pattern string, i int) bool {
	if node == nil {
		return false
	}
	if i == len(pattern) {
		return node.Val != nil
	}

	c := pattern[i]

	// 不是通配符，则从对应的子节点开始匹配
	if c != '.' {
		return t.hasKeyWithPattern(node.Children[int(c)], pattern, i+1)
	}
	// 是通配符，尝试匹配所有字符
	for j := 0; j < R; j++ {
		if t.hasKeyWithPattern(node.Children[j], pattern, i+1) {
			return true
		}
	}

	return false
}

// Put 在 map 中添加或修改键值对
func (t *TrieMap) Put(key string, val interface{}) {
	if !t.ContainsKey(key) {
		t.Size++
	}
	t.root = t.put(t.root, key, val, 0)
}

// 向以 node 为根的 Trie 树中插入 key[i..]，返回插入完成后的根节点
func (t *TrieMap) put(node *TrieNode, key string, val interface{}, i int) *TrieNode {
	// 如果树枝不存在，新建
	if node == nil {
		node = new(TrieNode)
	}
	// key 的路径已插入完成，将值 val 存入节点
	if i == len(key) {
		node.Val = val
		return node
	}

	c := key[i]
	// 递归插入子节点，并接收返回值
	node.Children[int(c)] = t.put(node.Children[int(c)], key, val, i+1)
	return node
}

// Remove 在 Map 中删除 key
func (t *TrieMap) Remove(key string) {
	if !t.ContainsKey(key) {
		return
	}
	t.root = t.remove(t.root, key, 0)
	t.Size--
}

func (t *TrieMap) remove(node *TrieNode, key string, i int) *TrieNode {
	if node == nil {
		return nil
	}
	// 找到了 key 对应的 TrieNode，删除 val
	if i == len(key) {
		node.Val = nil
	} else {
		c := key[i]
		node.Children[int(c)] = t.remove(node.Children[int(c)], key, i+1)
	}

	if node.Val != nil {
		return node
	}
	for c := 0; c < R; c++ {
		if node.Children[c] != nil {
			return node
		}
	}

	return nil
}
```