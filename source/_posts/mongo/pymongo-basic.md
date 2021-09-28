---
title: PyMongo 基本概念和操作
categories:
  - MongoDB
tags:
  - MongoDB
index_img: /img/mongodb.jpg
date: 2021-09-28 15:01:25
---

# PyMongo 基本概念和操作

最近尝试用数据库来给数据集提前储存 encode 好的 embeddings，原本想用 Sqlite 的，但是想到各种储存列表、大数值向量都很麻烦，于是去试了试非关系型数据库 MongoDB，发现用的是真的方便真的爽。

本文通过 MongoDB 的 python 接口例子来说明常用的一些使用方法。

## MongoDB 基本概念

一篇写的不错的知乎文章：https://zhuanlan.zhihu.com/p/87722764

### 数据库基本模型

| MongoDB 概念 | 对应的 SQL 概念 | 备注                                          |
| ------------ | --------------- | --------------------------------------------- |
| database     | database        | 一个数据库包含多个集合（collection）          |
| collection   | table           | 可以储存多个文档，且没有模式限制              |
| document     | row             | 由多个字段表示，使用的 BSON 格式（类似 JSON） |
| field        | column          | 字段可储存任意数据格式，如列表                |

### BSON 数据类型

BSON，Binary JSON，不能说跟 JSON 很像，只能说是一模一样。（并不，实际上多了索引等数据，且支持的数据类型也更多）

```json
{
    "_id": 1,
    "name": {"first": "John", "last": "Backus"},
    "contribs": ["Fortran", "ALGOL", "Backus-Naur Form", "FP"],
    "awards": [
        {
            "award": "W.W. McDowell Award",
            "year": 1967,
            "by": "IEEE Computer Society"
        },
        {
            "award": "Draper Prize",
            "year": 1993,
            "by": "National Academy of Engineering"
        }
    ]
}
```

其中，`_id` 字段是一条记录的主键，在 collection 中是唯一的，可以由系统自动生成（类型为 `ObjectId`），也可以自己指定。

## PyMongo 基本操作

记得先安装并 import 哦。

```python
import pymongo
```

### 数据库操作

#### 连接数据库服务器（无密码）

默认端口号是 27017

```python
client = pymongo.MongoClient("127.0.0.1", 27017)
```

#### 获取具体的数据库、集合

```python
client = pymongo.MongoClient("127.0.0.1", 27017)

# 获取名字为 udc 的数据库
db = client["udc"]

# 获取数据库中两个集合
example_collection = db["example"]
candidate_collection = db["candidate"]
```

如果数据库或集合未被创建，则会新建一个。（实际的创建行为会在写入第一个记录时才发生）

### CRUD 操作

以在某个 collection 上的查询为例子，操作非常简单，指定对应的字段名即可。

```python
example_collection.find_one({"_id": 100012})
```

其他具体操作请查阅 MongoDB 官方文档：https://docs.mongodb.com/manual/crud/

以及 pymongo 的示例：https://pymongo.readthedocs.io/en/stable/tutorial.html

### 聚合操作

聚合（aggregation）稍微复杂，重要的**关联查询**就是使用这种操作完成的。MongoDB 中，可以给一系列的聚合操作定义一个 pipeline，像流水线一样层层过滤和组装，得到最终结果。

流水线通常是一个字典列表， 列表中每一个字典都是一个聚合操作，字典的 key 就是下面这些 $ 开头的字符串，对应的 value 是具体的参数字典。PyMongo 的一个例子：

```python
pipeline = [
    {
        "$sample": {   # 操作名称为 $sample
            "size": 3  # 参数名 size 的值为 3
        }
    },
    ...
]
```

#### 常用的一些聚合操作类型

| 命令     | 描述                       |
| -------- | -------------------------- |
| $project | 指定输出文档中的字段       |
| $match   | 相当于 query 语句          |
| $limit   | 限制传递给下一步的文档数量 |
| $lookup  | 多表关联查询               |
| $sample  | 随机取样                   |
| $group   | 根据 key 分组文档          |
| $sort    | 根据指定的字段排序文档     |

具体见：https://docs.mongodb.com/manual/core/aggregation-pipeline/

#### 关联查询 `$lookup`

语法：

```json
"$lookup": {
    "from": "candidate",
    "localField": "candidate_ids",
    "foreignField": "_id",
    "as": "candidates"
}
```

| 参数名       | 说明           |
| ------------ | -------------- |
| from         | 关联的外表     |
| localField   | 本表的关联字段 |
| foreignField | 外表的关联字段 |
| as           | 输出的字段值   |

所以上面的语句就是我现在在 `example` 集合（本表）中，跑去看名为 `candidate` 的集合（外表），将本表中 `candidates_ids` 字段中与外表的 `_id` 字段进行关联，相同的值的外表文档输出到一个列表里，插入到本表文档中，字段名为 `candidates` 。

在我的例子中，我的 `candidates_ids ` 本身就是一个列表，里面仅储存了另一个集合的主键（相当于外键），因此关联后返回一个列表，里面的元素是外表中的文档。例如：

```json
_id:1100142
tag:"dev"
context:Array
response_id:"9ZOANIYSNBUL"
candidate_ids:Array
cadidates:Array
```

#### PyMongo 使用流水线进行聚合的示例

```python
pipeline = [
        {
            "$match": {
                "_id": 1105000
            }
        },
        {
            "$sample": {
                "size": 5
            }
        },
        {
            "$lookup": {
                "from": "candidate",
                "localField": "candidate_ids",
                "foreignField": "_id",
                "as": "candidates"
            }
        },
        {
            "$lookup": {
                "from": "candidate",
                "localField": "response_id",
                "foreignField": "_id",
                "as": "response"
            }
        }
    ]

    with example_collection.aggregate(pipeline, batchSize=batch_size) as cursor:
        for item in cursor:
            print(item["tag"])
```

定义 `pipeline` 为聚合操作的列表，然后在 `example_collection` 集合上进行关联查询，返回一个 `cursor` 对象。可以直接遍历这个对象拿到值。

### `cursor` 对象

实际上是一个用于遍历 MongoDB 查询结果的工具。详见：https://pymongo.readthedocs.io/en/stable/api/pymongo/cursor.html

具体用法有空另开一篇写，这篇就先到这儿了。
