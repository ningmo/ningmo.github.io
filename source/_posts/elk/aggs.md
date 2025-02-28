---
title: elasticsearch aggs方式
tags: [elasticsearch, aggs]
date: 2019-08-27 22:18:00
categories: [elasticsearch]
---

以下内容基于ES7

a为text d为keyword  e为text(fielddata=true)
```json
{
  "mappings": {
    "properties": {
        "a":{
          "type":"text"
        },
        "d":{
          "type":"keyword"
        },
        "e":{
          "type":"text",
          "fielddata": true
        }
    }
  }
}
```

### 1. terms aggs
- 对精确值keyword(不可分词)字段的分组 keyword或text.keyword
- 对设置fielddata=true的text字段进行aggs 将对字段值进行分词后的结果进行aggs 
进行aggs查询
```json
{
  "aggs":{
    "txt":{
      "terms":{
        "field": "a.keyword"
      }
    },
    "k":{
      "terms":{
        "field": "d"
      }
    },
    "kf":{
      "terms":{
        "field": "e"
      }
    }
  }
}
```
- top_hits 获取aggs后bucket中命中的key的文档
```json
{
  "aggs":{
      "by_e":{
        "terms":{
            "field":"e"
        },
        "aggs": {
          "most_group": {
            "top_hits": {
              "size": 10,
              "sort": [
                {
                  "i":{
                    "order": "desc"
                  }
                }
                ]
            }
          }
        }
    }
  }
}
```
- 性能 打开分词字段的eager_global_ordinals(在mapping中的type下面添加)*
### 2. metric aggs 分组统计
- min/max/avg/sum/stats 
- cardinate 类似于mysql的count(distinct(字段))

```json
{
  "aggs":{
      "count_distinct":{
        "cardinality":{
            "field":"e"
        }
    }
  }
}
```

### 3. range aggs 区间范围分组
### 4. histogram aggs 对指定范围内的等差区间进行aggs

### 5.pipeline  对terms嵌套metric的结果进行进一步metric
- min_bucket/max_bucket/stats_bucket/avg_bucket/sum_bucket
- derivative...