---
title: mysql_trigger_and_procedure
categories: []
date: 2013-07-31 14:22:00
---

##### 触发器
catabig为表名
所操作的数据： 添加的数据或修改后的数据为new  删除或修改前的数据为old
触发器需要避免死循环
如 insert操作触发触发器的insert操作 依次无限循环下去导致死循环

···
mysql> delimiter //
mysql> create trigger mytrigger before update
    ->  on catabig for each row
    -> begin
    ->  set new.t=concat(new.t,old.t);
    -> end//
Query OK, 0 rows affected (0.04 sec)

mysql> delimiter ;
mysql> select * from catabig;
+----+------+
| id | t    |
+----+------+
|  3 | PHP  |
+----+------+
3 rows in set (0.00 sec)
mysql> update catabig set t='_upd' where id=3;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
mysql> select * from catabig;
+----+---------+
| id | t       |
+----+---------+
|  3 | _updPHP |
+----+---------+
3 rows in set (0.00 sec)
···

##### 存储过程

···
delimiter //
create procedure mypro(ed int)
begin
	declare i int;  -- 定义声明必须在最前面
	declare rs  int;
	set rs=0 ;
	set i=1 ;
	label1:loop
		if i<=ed	then
			set rs=i+rs;
			set i=i+1;
			ITERATE label1;  -- 继续循环
		else leave label1;   -- 结束循环
		end if;
	end loop label1;             -- 结束循环
	select rs;      -- 不允许存在return（只有function可以有return）
end  //
delimiter ;
 -- 调用
mysql> call mypro(100);
+------+
| rs   |
+------+
| 5050 |
+------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
···
