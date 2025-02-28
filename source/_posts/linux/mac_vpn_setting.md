---
date: 2022-03-31T22:01:25
title: mac下vpn转发指定流量(L2TP)
tags: [linux,centos]
categories: nginx
---

在/etc/ppp/ip-up(没有则创建文件)脚本中写入转发规则
```
#!/bin/bash

# 单个指定
/sbin/route add -net 192.168.70.11 -interface $1   
# 范围指定
/sbin/route add -net 192.168.80.0、24 -interface $1

# 删除默认路由	
/sbin/route delete default

# 添加默认路由为指定的地址
/sbin/route add default 192.168.1.1
```
增加执行权限
```
sudo chmod u+x /etc/ppp/ip-up
```
将vpn的网络顺序设置后最后一个 降低优先级
辅助命令
```
# 查看路由表
netstat -nr

# 查看dns
nslookup store.chanjet.com
```



以上内容引用自 [macOS设置L2TP类型的VPN转发指定的流量](https://pocketcloud.vip/2020/03/19/macOS%E8%AE%BE%E7%BD%AEL2TP%E7%B1%BB%E5%9E%8B%E7%9A%84VPN%E8%BD%AC%E5%8F%91%E6%8C%87%E5%AE%9A%E7%9A%84%E6%B5%81%E9%87%8F/)
