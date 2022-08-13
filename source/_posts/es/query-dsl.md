---
title: Query DSL
date: 2022-08-13 19:56:00
categories:
  - Elasticsearch
index_img: /img/es.png
---

# Query DSL

Query DSL (Domain Specific Language) 是 ES 提供的基于 JSON 的查询语言。

推荐参考：[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)

## Query 和 Filter 上下文

### Query Context

用于解答文档是否存在，并且告知返回文档与查询条件的匹配度，返回_score 评分供用户选择。

### Filter Context

+ 只用于返回文档是否与查询匹配，但是不会告诉你匹配度，即不进行评分。
+ 在做聚合查询时，filter 经常发挥更大的作用，因为没有评分 Elasticsearch 的处理速度就会提高，提升了整体响应时间
+ filter 可以缓存查询结果，而 Query 则不能缓存

### 使用场景

如果涉及到全文检索以及评分相关业务使用 Query，其他场景推荐使用 Filter 查询。

## 组合查询

### bool 查询

bool 查询包括四种: must, should, must_not, filter 子句。其中 must 和 should 属于 query context，换言之会返回评分；而 must_not 和 filter 属于 filter context，它们将不符合条件的文档直接过滤掉，因此不影响评分。

bool 查询根据 must 和 should 子句匹配的越多，分数越高。

#### must

子句中的值一定会出现在匹配文档，且会返回评分。

#### filter

子句中的值一定会出现在匹配文档，但不返回评分。

#### should

子句的值应当出现在文档中，类似于关系型数据库中的 or。

如果一个 bool 查询中只有 should 子句，则至少需要满足其中一个（可以通过 `minimum_should_match` 参数修改这个值，默认为 1）。

#### must_not

子句中的值一定不会出现在匹配文档中。

### boosting 查询

匹配到 positive 子句的文档分数会更高，而匹配了 negative 子句的文档分数会降低。

### constant_score 查询

对匹配的文档返回完全一样的分数。（即无视词频，有匹配即可）

### dis_max 查询

Disjunction max query 查询也被理解为分离最大化查询，指的是将任何与任一查询匹配的文档，作为结果返回，但只将**最佳匹配的评分**作为查询的评分。

### function_score 查询

Function score 允许你控制查询评分，是用来控制评分过程的终极武器。

## 全文查询

此处略过，详见[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/full-text-queries.html)。

## Term - Level 查询

可以使用 Term - level 查询结构化数据，结构化数据如日期范围、IP 地址、价格等。Term - level 查询不分析查询项的内容，需要精确匹配字段。

### exists 查询

返回存在某个**字段**的文档。

下面几种情况下的文档不会被返回：

+ 原始 JSON 结构中字段值为 `null` 或 `[]`
+ 在 mapping 中设置了 `"index" : false` 的字段
+ 字段值的长度超过了 mapping 中 `ignore_above` 设定的值
+ 字段值是畸形的，且 mapping 中设置了 `ignore_malformed`

### fuzzy 查询

返回包含与搜索字词相似的字词的文档，可以用于查询纠错功能。基于词的最小编辑距离。

### ids 查询

范围查询包含 ID 的文档。

### prefix 查询

返回在提供的字段中包含特定前缀的文档。

### range 查询

查询类似数据库中的大于、小于范围查询。可以对数值字段、日期字段生效。

+ gt：大于
+ gte：大于等于
+ lt：小于
+ lte：小于等于

### regexp 查询

正则表达式查询。

### term 查询

返回确切的文档内容。避免对 text 字段类型使用 term。

### terms 查询

返回一个或多个包含精确查询条件的文档信息。

### terms_set 查询

返回最小精确匹配成功的文档信息，terms_set 类似 terms 查询，只不过terms_set 多定义了返回最小匹配的数量。

### wildcard 查询

通配符（wildcard）查询，返回包含与通配符模式匹配的术语的文档。