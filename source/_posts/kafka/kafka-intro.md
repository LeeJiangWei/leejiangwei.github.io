---
title: Kafka基础
date: 2023-04-27 16:31:11
categories:
- Kafka
---

# Kafka基础

## 基本概念

+ broker: kafka 集群中的一台服务器
+ topic: 同一类型消息的集合
+ partition: 每个主题都划分为多个分区，保存在不同服务器上。每个分区都是 append log 文件。避免磁盘爆炸，顺便负载均衡
+ offset: 一个分区对应了一个文件，某条消息在文件中的位置就是偏移量，为一个 long 型数据

## 流程

### 生产

生产者要写入一条记录，可以指定四个参数：分别是 topic、partition、key 和 value，其中 topic 和 value（要写入的数据）是必须要指定的，而 key 和 partition 是可选的。

对于一条记录，先对其进行序列化，然后按照 Topic 和 Partition，放进对应的发送队列中。

如果 Partition 没填，那么情况会是这样的：

+ Key 有填：按照 Key 进行哈希，相同 Key 去一个 Partition
+ Key 没填：Round-Robin 来选 Partition

### 消费

对于消费者，不是以单独的形式存在的，每一个消费者属于一个 consumer group，一个 group 包含多个 consumer。订阅 Topic 是以一个消费组来订阅的，发送到 Topic 的消息，只会被订阅此 Topic 的每个 group 中的一个 consumer 消费。

如果所有的 Consumer 都具有相同的 group，那么就像是一个点对点的消息系统；如果每个 consumer 都具有不同的 group，那么消息会广播给所有的消费者。

具体说来，这实际上是根据 partition 来分的，一个 Partition，只能被消费组里的一个消费者消费，但是可以同时被多个消费组消费。

消费组里的每个消费者是关联到一个 partition 的，因此有这样的说法：对于一个 topic,同一个 group 中不能有多于 partitions 个数的 consumer 同时消费,否则将意味着某些 consumer 将无法得到消息。

同一个消费组的两个消费者不会同时消费一个 partition。

在 kafka 中，采用了 pull 方式，即 consumer 在和 broker 建立连接之后，主动去 pull (fetch) 消息。
