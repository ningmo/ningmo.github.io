---
title: search suggest
tags: [elasticsearch]
date: 2019-08-26 21:33:35
---


### 1. term suggest 常用于拼写错误
```
POST /kibana_sample_data_logs/_search
{
  "suggest":{
    "term-suggestion":{
      "text":"elastia",
      "term":{
        "suggest_mode":"popular",
        "field":"url"
      }
    }
  }
}
```
result:
```json
{
  "took" : 65,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 0,
      "relation" : "eq"
    },
    "max_score" : null,
    "hits" : [ ]
  },
  "suggest" : {
    "term-suggestion" : [
      {
        "text" : "elastia",
        "offset" : 0,
        "length" : 7,
        "options" : [
          {
            "text" : "elastic",
            "score" : 0.85714287,
            "freq" : 2724
          }
        ]
      }
    ]
  }
}
```
suggest_mode
- missing 如索引中已存在,就不提供建议
- popular 推荐出现频率更高的词
- always 无论是否存在 都提供建议 

### 2. phrase suggest

### 3.completion suggest 自动不全
需要先定义mapping 字段设置type为 completion

```
PUT completion_index_a
{
  "mappings": {
    "properties": {
      "yourfield":{
        "type": "completion"
      }
    }
  }
}
```
result
```json
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "completion_index_a"
}
```
使用
```
POST /completion_index_a/_search
{
  "suggest":{
    "comp-suggestion":{
      "prefix":"n",
      "completion":{
        "field":"yourfield"
      }
    }
  }
}
```
### 4..context suggest 上下文建议