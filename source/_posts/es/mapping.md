---
title: 理解 mapping
date: 2022-08-13 17:56:00
categories:
  - Elasticsearch
index_img: /img/es.png
---

# 理解 mapping

映射（mapping）就像数据库中的 Schema ，描述了文档可能具有的字段或属性、每个字段的数据类型，比如 Text，Keyword，Integer 或 Date ，以及 Lucene 是如何索引和存储这些字段的。

## 核心简单字段类型

Elasticsearch 支持如下简单字段类型：

+ 字符串： text，keyword
+ 整数：byte，short，integer，long
+ 浮点数： float，double
+ 布尔型： boolean
+ 日期： date

更多的字段类型比如 geo_point，ip，nested 等可以在链接处查看：https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html

查看索引的映射：`GET twitter/_mapping`

## 倒排索引

+ 适用于快速的全文检索
+ Elasticsearch 文档每个字段都会被索引。如果某些字段不需要支持查询，可以在映射中配置 "index": false ，减少存储空间占用，并且提升写入速度

例如：文章的标题需要创建索引，而 url 字段不需要被索引。创建索引映射时可以按以下方式禁用它：

```json
PUT news
{
    "settings": { 
        "number_of_shards": 5, 
        "number_of_replicas": 1
    },
    "mappings": { 
        "properties": { 
            "title": { 
                "type": "text", 
                "analyzer": "ik_max_word"
            },
            "url": {
                "type": "keyword", 
                "index": false
            }   
        }
    }
}
```

## 文档值

在 Elasticsearch 中，Doc Values 是一种列式存储结构。

+ Doc Values 默认对所有字段启用，除了 text 和 annotated_text 类型字段
+ Doc Values 是在索引时创建的，当字段索引时，Elasticsearch 为了能够快速检索，会把字段的值加入倒排索引中，同时它也会存储该字段的 Doc Values

Elasticsearch 中的 Doc Values 常被应用到以下场景：

+ 对一个字段进行排序
+ 对一个字段进行聚合
+ 某些过滤，比如地理位置过滤
+ 某些与字段相关的脚本计算
+ 使用 docvalue_fields 返回搜索结果部分字段值

注：笔者这里理解它为 ES 为了加速查询和排序等操作的特殊储存方式，同类型的字段可以被高效地压缩储存。（而不是储存 JSON 原文）

+ 如果你知道你永远也不会对某些字段进行聚合、排序或是使用脚本操作，你可以通过禁用特定字段的 Doc Values。这样不仅节省磁盘空间，也会提升索引的速度。
+ 要禁用 Doc Values，在字段的映射设置 doc_values: false

```json
PUT my_index
{ 
    "mappings": { 
        "properties": { 
            "session_id": { 
                "type": "keyword", 
                "doc_values": false
            }
        }
    }
}
```

## 储存

默认情况下，字段原始值会被索引用于查询，但是不会被存储。

+ 在字段的映射 (mapping) 设置 "store": true，可以使索引单独保存这个字段。
+ 通常情况下，如果文档本身十分庞大，而一些字段又会经常单独使用，那么这样的字段，就可以设置为单独存储，然后可以使用 stored_fields 单独检索这些字段。
+ 例如，如果你的文档包含标题、时间和一个很大的正文字段，你可能只需要检索标题、时间字段，没必要从很大的 _source 原文中解析出这些字段。

## 原文

+ _source 字段包含索引时发送的原始 JSON 文档。可以通过设置，禁用原文字段，或者只存储特定字段。
+ _source 字段会导致占用更多的存储空间。如果业务上不需要存储原始文档，可以按以下方式禁用它。
+ 禁用 _source 会导致更新、重建索引、摘要功能不可用，生产环境慎用。考虑节省存储空间，可以通过修改索引设置 index.codec 提高压缩效率。