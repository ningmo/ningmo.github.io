---
title: mysql 中间件
categories: [mysql]
date: 2015-10-30 09:36:17
---

360的Atlas

1.读写分离

2.从库负载均衡

3.IP过滤

4.自动分表

5.DBA可平滑上下线DB

6.自动摘除宕机的DB

altas 在10000/s的请求量级应该是毫无问题的

https://github.com/Qihoo360/Atlas/blob/master/README_ZH.md

&nbsp;

kingshard（GO）

1. 读写分离。
2. 跨节点分表。
3. 支持透明的MySQL连接池，不必每次新建连接。
4. 支持客户端IP访问控制。
5. 平滑上线DB或下线DB，前端应用无感知。
6. 支持多个slave，slave之间通过权值进行负载均衡。
7. 支持强制读主库。
8. 支持将sql发送到特定的node。
9. 支持在单个node上执行事务，不支持跨多个node执行事务。
10. 支持跨node的count,sum,max和min等函数。
11. 支持单个分表的join操作，即支持分表和另一张不分表的join操作。
12. 支持order by,limit等操作。
13. 支持主流语言（java,php,python,C/C++,Go)SDK的mysql的prepare特性。

https://github.com/flike/kingshard/blob/master/README_ZH.md

&nbsp;

阿里的Cobar（java）

https://github.com/alibaba/cobar

淘宝的 TDDL

https://github.com/alibaba/tb_tddl

百度的DBProxy

Amoeba

http://www.biaodianfu.com/amoeba.html

mysqlnd_ms

读写分离插件http://www.laruence.com/2011/10/05/2192.html

mysql官方的mysql-proxy

OneProxy

OneSQL
