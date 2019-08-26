---
date: 2013-01-31T10:50:28

title: sql快速生成大量数据

---

```先插入一条数据
insert into table(a,b,c,d) values(1,2,3,4)
然后重复执行以下sql语句  没执行一次 数据就会倍增
insert into table(a,b,c,d) (select a,b,c,d from table)```
<em>注意：上句中为避免可能有自增主键的存在而没有使用（*），还有使用时注意唯一性约束导致报错。</em>
