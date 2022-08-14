---
title: 复杂数据类型
date: 2022-08-14 14:15:00
categories:
  - Elasticsearch
index_img: /img/es.png
---

# 复杂数据类型

https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html

## Object 类型

Object 类型就是嵌套 JSON。

这个例子中，这个文档包含一个名为 `manager` 的 object ，而 `manager` 又包含了一个叫 `name` 的 object 。

```json
PUT my-index-000001/_doc/1
{ 
  "region": "US",
  "manager": { 
    "age":     30,
    "name": { 
      "first": "John",
      "last":  "Smith"
    }
  }
}
```

实际查询时，使用 `.` 选择即可。

```json
{
  "region":             "US",
  "manager.age":        30,
  "manager.name.first": "John",
  "manager.name.last":  "Smith"
}
```

## Join 类型

Join 类型定义了同一个索引中的父子结构关系。

```json
PUT my-index-000001/_doc/1?refresh
{
  "my_id": "1",
  "text": "This is a question",
  "my_join_field": {
    "name": "question" 
  }
}

PUT my-index-000001/_doc/3?routing=1&refresh 
{
  "my_id": "3",
  "text": "This is an answer",
  "my_join_field": {
    "name": "answer", 
    "parent": "1" 
  }
}
```

> We don’t recommend using multiple levels of relations to replicate a relational model. Each level of relation adds an overhead at query time in terms of memory and computation. For better search performance, denormalize your data instead.

> 我们不建议使用多级关系来复制关系模型。在内存和计算方面，每个级别的关系都会在查询时增加开销。为了获得更好的搜索性能，请改为对数据进行非规范化 (denormalize)。

只有当父子关系的其中一种远多于另一种时才适合使用。

## Nested 类型

Nested 类型是 Object 的专用版本，允许对象数组被索引，且能独立查询。

默认情况下，如果在 mapping 中没有指定， ES 储存对象数组时会将数组“扁平化”。例如：

```json
PUT my-index-000001/_doc/1
{
  "group" : "fans",
  "user" : [ 
    {
      "first" : "John",
      "last" :  "Smith"
    },
    {
      "first" : "Alice",
      "last" :  "White"
    }
  ]
}
```

在 ES 内部，数据会变成类似这样的格式：

```json
{
  "group" :        "fans",
  "user.first" : [ "alice", "john" ],
  "user.last" :  [ "smith", "white" ]
}
```

可以看到， Alice 和 White 的关联关系丢失了，下面的这个查询会返回不正确的结果。

```json
GET my-index-000001/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "user.first": "Alice" }},
        { "match": { "user.last":  "Smith" }}
      ]
    }
  }
}
```

因此如果需要储存独立的对象数组，需要在 mapping 中指定 `"type": nested`

```json
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "user": {
        "type": "nested" 
      }
    }
  }
}

PUT my-index-000001/_doc/1
{
  "group" : "fans",
  "user" : [
    {
      "first" : "John",
      "last" :  "Smith"
    },
    {
      "first" : "Alice",
      "last" :  "White"
    }
  ]
}
```

## Flattened 类型

默认情况下，一个 object 的字段是独立映射和索引的，如果没有实先指定字段，则它们会被动态索引。

Flattened 类型提供另一种行为，它将整个 object 都当作一个字段，然后每个子字段都被当成 keyword 类型处理。

> This data type can be useful for indexing objects with a large or unknown number of unique keys.

> 当一个 object 有大量或未知数量的 key 时可以使用这种结构。

```json
PUT bug_reports
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text"
      },
      "labels": {
        "type": "flattened"
      }
    }
  }
}

POST bug_reports/_doc/1
{
  "title": "Results are not sorted correctly.",
  "labels": {
    "priority": "urgent",
    "release": ["v1.2.5", "v1.3.0"],
    "timestamp": {
      "created": 1541458026,
      "closed": 1541457010
    }
  }
}
```

在 flattened 的顶层搜索会自动搜索所有叶子节点。

```json
POST bug_reports/_search
{
  "query": {
    "term": {"labels": "urgent"}
  }
}
```

也可以使用 `.` 运算符来访问 object 的子字段。

```json
POST bug_reports/_search
{
  "query": {
    "term": {"labels.release": "v1.3.0"}
  }
}
```
