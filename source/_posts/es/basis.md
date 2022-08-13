---
title: Elasticsearch (ES) 基础
date: 2022-08-13 16:30:00
categories:
  - Elasticsearch
index_img: /img/es.png
---

# Elasticsearch (ES) 基础

## ES 集群架构

### Cluster（集群）

Cluster 就是集群的意思。

![cluster](https://s2.loli.net/2022/08/13/6hHw4IJTxZnyPYu.png)

+ Elasticsearch 集群由一个或多个节点组成，可通过其集群名称进行标识
+ 获取集群状态：`GET /_cluster/state`

### Node（节点）

单个 Elasticsearch 实例。

+ 一个集群由一个或多个 node 组成
+ 在大多数环境中，每个节点都在单独的盒子或虚拟机上运行

### Document（文档）

Elasticsearch 中索引或搜索的最小数据单元。

+ 文档通常是数据的 JSON 表示形式

### Index（索引）

在 Elasticsearch 中，index 是 document 的集合。

+ 每个 Index 一个或许多的 documents 组成，并且这些 document 可以分布于不同的 shard 之中

与关系型数据库的索引不同，ES 中，索引表示两个概念：

+ 名词：文档的集合
+ 动词：储存一个文档的过程，例：索引一个文档 ~= 储存一个文档

### Shard（分片）

Elasticsearch 是一个分布式搜索引擎，因此 index 通常会拆分为分布在多个节点上的 shard。

![index.png](https://s2.loli.net/2022/08/13/ZOYqxculhskjzWA.png)

有两种类型的分片：Primary shard (主分片) 和 Replica shard (副本分片)。
只有主分片可以接受索引请求，副本和主分片都可以提供查询请求。

+ 主分片：
    + 每个文档都存储在一个 Primary shard
    + 索引文档时，它首先在 Primary shard 上编制索引，然后在此分片的所有副本上（replica）编制索引。
    + 索引可以包含一个或多个主分片
+ 副本分片：
    + 增加故障转移：如果主分片故障，可以将副本分片提升为主分片
    + 提高性能：get 和 search 请求可以由主分片或副本分片处理。

![shard](https://s2.loli.net/2022/08/13/x3hX8GRBVnmskUf.png)

+ 一个 index 是一个逻辑命名空间，它映射到一个或多个主分片，并且可以具有零个或多个副本分片
+ 分配数量和 index 有关，而不是整个集群。每个 index 的分片数量可以独立设置

获取名为 twitter 的索引的配置：`GET /twitter/_settings?pretty`


#### Index 健康状况

查看索引健康状况：`GET /_cat/indices/twitter`

+ 红色：集群中未分配至少一个主分片
+ 黄色：已分配所有主副本，但未分配至少一个副本（此情况下，如果主分片故障，数据会全部丢失）
+ 绿色：分配所有分片
