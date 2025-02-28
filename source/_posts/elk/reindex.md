---
title: elasticsearch reindex
date: 2019-08-29 21:57:14
tags: [elasticsearch]
categories: [elasticsearch]
---

## 适用reindex的情景
### 1. mapping发生变更 字段类型变更 分词器及字典更新
### 2. setting发生变更 主分片数量发生变更
### 3. 数据迁移


## 1. update by query 在现有索引上重建
- 对已有增加增加子字段/增加分词器
```json
{
  "properties": {
    "date":{
      "type":"keyword",  
      "fields": {
        "indate":{
          "type":"text",
          "analyzer":"english"
        }
      }
    }
  }
}
```
然后执行
```shell script
POST /my_blogs/_update_by_query
```

## 2. reindex 在其他索引上重建
需要开启白名单并重启节点
```shell script
POST _reindex
{
  "source": {
    "index": "my_blogs"
  },
  "dest": {
    "index": "new_blogs"
  }
}
```
同时还支持 筛选数据重建/跨集群重建

如果重建耗时较长 还支持通过任务id查看进度
更多参数详见官方文档


*以上要求mapping的_source为enabled*