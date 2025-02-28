---
title: mysql查看状态语句
categories: [mysql]
date: 2015-11-26 21:22:04
---

<code>```
show status;

show [session|global] status like 'com_insert|com_update...';

show status like 'connerctions';

show status like 'slow_queries';

    show variables like 'long_query_time';



--开启慢查询日志 启动参数

mysqld  --safemode --slow-query-log

或者 mysqld -log-slow-queries=d/abc.log
```
</code>
