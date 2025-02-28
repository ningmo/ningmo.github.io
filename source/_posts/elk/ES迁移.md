---
title: ES集群迁移
tags: [elasticsearch]
categories: [elasticsearch]
date: 2023-12-06 21:33:35
---

1.修改ES配置文件  增加仓库配置目录配置 并给ES写入的权限
```
path.repo: ["/usr/share/elasticsearch/backup"]  #目录必须是ES可以访问的目录 如果是集群 需要全部数据节点配置上
```
2.创建仓库
```
PUT /_snapshot/backup -H 'Content-Type: application/json' -d '
{
   "indices":"source_index",
   "type": "fs",
   "settings": {
        "location": "/usr/share/elasticsearch/backup",
        "compress": true
   }
}'
```
3.创建快照
```
PUT /_snapshot/backup/your_snapshot_name -H 'Content-Type: application/json' -d '
{
  "indices": "source_index",
  "ignore_unavailable": true,
  "include_global_state": false
}'
```
可以通过```/_snapshot/_status```查看进度

4.在目标集群按照步骤1、2 创建仓库，然后将快照文件拷贝到仓库目录下
5.在目标集群上将快照还原
```
POST /_snapshot/backup/your_snapshot_name/_restore
{
  "indices": "dest_index_name",
  "ignore_unavailable": true,
  "include_global_state": false
}
```

可能的异常：

1.`path.repo`配置没有部署到所有节点

```[backup] location [/usr/share/elasticsearch/backup] doesn't match any of the locations specified by path.repo because this setting is empty```

2.报错`failed to create blob container`是目录权限问题
