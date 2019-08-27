---
title: search template
tags: [elasticsearch]
date: 2019-08-25 21:33:35
---

创建搜索模板
```
POST _scripts/.kibana_1
{
  "script":{
    "lang": "mustache",
    "source":{
      "_source":["name","description","color"],
      "size":20,
      "query":{
        "multi_match": {
          "query": "{{q}}",
          "fields": ["name","description"]
        }
      }
    }
  }
}
```

```
# 查看
GET _scripts/.kibana_1
# 查看删除模板
DELETE _scripts/.kibana_1
```
使用
```
POST searchTplA/_search/template
{
  "id":".kibana_1",
  "params": {
    "search":"a"
  }
}
```