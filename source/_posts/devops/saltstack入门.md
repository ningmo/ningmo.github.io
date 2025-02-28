---
title: saltstack入门
categories: [saltstack]
date: 2015-08-10 17:25:59
---

主master
```
yum install salt-master salt-master -d
```
客户端client
```shell
yum install salt-minion
```
在客户端的/etc/salt/minion 里面填写主的ip地址
salt-minion-d
客户端启动后会主动请求服务端是否信任
服务端确认是否添加信任

```
salt-key -A # 全部添加
salt-key -a '...' #添加信任某个主机
salt-key -d '...' #删除信任某个主机
```

列出信任
```
salt-key -L
```

给客户端定义组
```
vim /etc/salt/master
```

命令格式 salt 【客户端】 【命令】 【参数】
过滤【客户端】
```
\* 全部
-G 'os:centos' 全部centos系统的
'10.10.102.*'
......
```

【命令】
```
cmd.run
test.ping
.......
```


自定义模块
```
cd /srv/salt/_modules/
vim m1.py
salt \* saltutil.sync_all # 同步至客户端
salt \* m1.run # 执行
```


参考http://rfyiamcool.blog.51cto.com/1030776/1262537
