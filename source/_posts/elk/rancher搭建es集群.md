---
title: 在rancher上搭建es7集群
tags: [elasticsearch, aggs]
date: 2020-06-24 14:20:00
categories: [elasticsearch]
---

平台环境 : rancher

es镜像 : docker.elastic.co/elasticsearch/elasticsearch:7.2.1

每个容器建一个磁盘并映射(注意用户权限)
配置与环境变量
```
cluster.name: es_cluster_brand
cluster.initial_master_nodes: node1
node.name: node1
node.master: true
node.data: true
path.data: /data
discovery.seed_hosts: node1,node2
http.cors.enabled: true
http.cors.allow-origin: "*"
http.port: 9200
network.publish_host: node1
network.bind_host: 0.0.0.0
network.host: 0.0.0.0
bootstrap.memory_lock: false
bootstrap.system_call_filter: false

cluster.name: es_cluster_brand
cluster.initial_master_nodes: node1
node.name: node2
node.master: true
node.data: true
path.data: /data
discovery.seed_hosts: node1,node2
http.port: 9200
http.cors.enabled: true
http.cors.allow-origin: "*"
network.publish_host: node2
network.bind_host: 0.0.0.0
network.host: 0.0.0.0
bootstrap.memory_lock: false
bootstrap.system_call_filter: false

cluster.name: es_cluster_brand
cluster.initial_master_nodes: node1
node.name: node3
node.master: true
node.data: true
path.data: /data
discovery.seed_hosts: node1,node2
http.port: 9200
http.cors.enabled: true
http.cors.allow-origin: "*"
network.publish_host: node3
network.bind_host: 0.0.0.0
network.host: 0.0.0.0
bootstrap.memory_lock: false
bootstrap.system_call_filter: false
```

然后建立域名映射到node1(主节点)
主机每个rancher实例的名字 都与其节点名字一致  防止出问题