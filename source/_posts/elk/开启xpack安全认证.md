---
title: es集群开启xpack安全认证
tags: [elasticsearch,xpack]
categories: [elasticsearch]
date: 2025-02-26 12:30:30
---

为安全起见，决定开启现有ES集群的重要操作安全校验，防止索引误删除

实现目标：在无认证的ES集群上开启安全认证，默认使用匿名用户，并设定匿名用户的权限，关键操作需要认证

1. 【所有节点】开启xpack 编辑`elasticsearch.yml`增加以下配置

```
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
# 匿名用户设定
xpack.security.authc:
  anonymous:
    username: "_anonymous"
    roles: ["anonymous_role"]
    authz_exception: true
```
并重启服务

2. 【主节点】设置密码

在主节点执行命令，按照提示输入各个elk组件访问ES的密码
```
bin/elasticsearch-setup-passwords interactive
```
然后执行
```
# 设置匿名用户角色的权限
curl -XPUT -H "Content-Type:application/json" -u elastic 127.0.0.1:9200/_security/role/anonymous_role -d '
{
  "indices": [
    {
      "names": ["*"],
      "privileges": ["read", "write", "create_index", "delete", "indices:data/bulk"]
    }
  ]
}'
```
详细权限见官方文档[https://www.elastic.co/guide/en/elasticsearch/reference/6.7/security-privileges.html#privileges-list-indices]


3. 【主节点】设置证书（不然ES会一直产生不信任警告日志）

```
bin/elasticsearch-certutil ca
bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12
```
会生成两个证书文件 `elastic-certificates.p12`、`elastic-stack-ca.p12`
```
mv elastic-* config/
chown elasticsearch config/elastic-* #### 非常重要 不然后面服务起不来
```
！！！！！！！！一定要设置两个文件的权限！！！！！！！！

4. 【所有节点】将前面的两个文件同步到其他节点的所有config下，并设置权限，然后重启所有节点的服务
