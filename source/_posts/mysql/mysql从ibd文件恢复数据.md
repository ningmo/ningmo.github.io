---
title: Mysql从ibd恢复数据
categories: [mysql]
date: 2019-10-21 15:21:34
---

1. 创建表结构
2. 删除表空间
```
alter table table_a discard tablespace;
```
3. 复制ibd文件到新库文件夹下 命名 table_a.ibd 修改文件权限 使mysql可以读写
4. 导入表空间
```
alter table table_a import tablespace;
```
如出现报错 
```Schema mismatch (Table has ROW_TYPE_DYNAMIC row format, .ibd file has ROW_TYPE_COMPACT row format.)```

则说明新建表与ibd表的row format不一致

删除新表 ,同时删除刚才拷贝过来的ibd文件 
重新建表时指定row format使一致,如
```ROW_FORMAT=COMPACT```

然后再次从步骤[3]开始进行