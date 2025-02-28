---
title: elasticsearch aggs 范围
tags: [elasticsearch, aggs]
categories: [elasticsearch]
date: 2019-08-28 21:24:22
---
## filter 多范围aggs
可以在一个查询内对多个范围的数据同时进行aggs
如下 对全局数据 以及满足filter条件的数据进行了aggs
```json
{
  "aggs":{
      "myFilter":{
        "filter":{
            "match": {
              "a": "abc"
            }
        },
        "aggs": {
          "by_myfilter": {
            "terms": {
              "field": "d"
            }
          }
        }
    },
    "to_all_aggs":{
      "terms": {
        "field": "d"
      }
    }
  }
}
```

### post filter 对aggs的结果进行filter 返回命中的文档
```json
{
  "aggs":{
      "by_d": {
        "terms": {
          "field": "d"
      }
    }
  },
  "post_filter":{
      "match": {
        "a": "abc"
      }
  }
}
```

### global 忽略query条件
```json
{
  "query": {
   "match": {
     "FIELD": "TEXT"
   }
  },
  "aggs":{
    "by_field":{
      "terms": {
        "field": "d",
        "size": 10
      }
    },
    "all":{
      "global": {},
      "aggs":{
          "by_d":{
            "terms": {
              "field": "",
              "size": 10
            }
          }
      }
    }
  }
}
```
