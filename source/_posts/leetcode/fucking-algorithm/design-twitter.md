---
title: 设计推特
categories:
  - LeetCode
  - Fucking
  - 数据结构设计
tags:
  - Go
  - 优先级队列
index_img: /img/code.jpg
sticky: -10
date: 2022-02-25 15:26:00
---
# 设计推特

https://leetcode-cn.com/problems/design-twitter/

此问题一个难点是如何按时间顺序展示 *关注了的用户* 发的推特。

每个用户用 **链表** 维护自己发的推特，这样可以保持推特的时间顺序信息。展示关注用户推特就相当于 [合并 K 个有序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)，用一个优先级队列实现即可。

用一个全局时间戳 `timestamp` 来记录推特的时间，用于优先级队列的排序，每次发推特都使这个值递增。推特类设计成类似链表的结构。

最后是 `Twitter` 对象要用一个哈希表维护自己的用户，然后每个用户也要用一个哈希表维护自己关注的用户 ID。

```go
type Twitter struct {
    UsersMap map[int]*User
}


func Constructor() Twitter {
    return Twitter{make(map[int]*User)}
}


func (this *Twitter) PostTweet(userId int, tweetId int)  {
    if u, ok := this.UsersMap[userId]; ok {
        u.post(tweetId)
    } else {
        u = NewUser(userId)
        this.UsersMap[userId] = u

        u.post(tweetId)
    }
}


func (this *Twitter) GetNewsFeed(userId int) []int {
    tweetIds := make([]int, 0)

    if u, ok := this.UsersMap[userId]; ok {
        pq := make(PriorityQueue, 0)
        heap.Init(&pq)

        for followee := range u.Following {
            headT := this.UsersMap[followee].Head
            if headT != nil {
                heap.Push(&pq, headT)
            }
        }

        // 通过优先级队列按时间降序取推文
        for pq.Len() > 0 {
            if len(tweetIds) == 10 {
                break
            }
            tweet := heap.Pop(&pq).(*Tweet)
            if tweet.Next != nil {
                heap.Push(&pq, tweet.Next)
            }

            tweetIds = append(tweetIds, tweet.Id)
        }
    }

    return tweetIds
}


func (this *Twitter) Follow(followerId int, followeeId int)  {
    // 如果 follower 或 followee 不存在，新建
    if _, ok := this.UsersMap[followerId]; !ok {
        f := NewUser(followerId)
        this.UsersMap[followerId] = f
    }
    if _, ok := this.UsersMap[followeeId]; !ok {
        f := NewUser(followeeId)
        this.UsersMap[followeeId] = f
    }
    this.UsersMap[followerId].follow(followeeId)
}


func (this *Twitter) Unfollow(followerId int, followeeId int)  {
    if f, ok := this.UsersMap[followerId]; ok {
        f.unfollow(followeeId)
    }
}


/**
 * Your Twitter object will be instantiated and called as such:
 * obj := Constructor();
 * obj.PostTweet(userId,tweetId);
 * param_2 := obj.GetNewsFeed(userId);
 * obj.Follow(followerId,followeeId);
 * obj.Unfollow(followerId,followeeId);
 */

/**** Tweet 类和 User 类 ****/

var timestamp int = 0

type Tweet struct {
    Id, Time int
    Next     *Tweet
}

type User struct {
    Id        int
    Following map[int]bool
    Head      *Tweet
}

func NewUser(userId int) *User {
    u := &User{userId, make(map[int]bool), nil}
    u.follow(userId)  // 把自己也加入关注列表
    return u
}

func (u *User) follow(userId int) {
    u.Following[userId] = true
}

func (u *User) unfollow(userId int) {
    if userId != u.Id {
        delete(u.Following, userId)
    }
}

func (u *User) post(tweetId int) {
    tweet := &Tweet{tweetId, timestamp, nil}
    timestamp++

    tweet.Next = u.Head
    u.Head = tweet
}

/**** 优先级队列 ****/

type PriorityQueue []*Tweet

func (pq PriorityQueue) Len() int { return len(pq) }

func (pq PriorityQueue) Less(i, j int) bool {
	// 按时间降序排列
	return pq[i].Time > pq[j].Time
}

func (pq PriorityQueue) Swap(i, j int) {
	pq[i], pq[j] = pq[j], pq[i]
}

func (pq *PriorityQueue) Push(x interface{}) {
	tweet := x.(*Tweet)
	*pq = append(*pq, tweet)
}

func (pq *PriorityQueue) Pop() interface{} {
	old := *pq
	n := len(old)
	tweet := old[n-1]
	old[n-1] = nil  // 防止内存泄漏
	*pq = old[:n-1]
	return tweet
}
```
