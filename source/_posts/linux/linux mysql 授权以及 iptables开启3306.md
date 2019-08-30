---
title: linux mysql 授权以及 iptables开启3306
categories:
  - mysql
  - linux
date: 2015-11-25 09:08:09
---

mysql授权ip段访问mysql

```
grant all privileges on *.* to 'yang'@'192.168.1.%' identified  by '123456';
```

linux iptables开启3306端口 访问
```
iptables -I INPUT -p tcp -s 192.168.1.0/24  --dport 3306 -j ACCEPT
/etc/init.d/iptables save
```

