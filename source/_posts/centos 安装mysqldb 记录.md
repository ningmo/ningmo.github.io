---
date: 2015-10-13T15:41:18

title: centos 安装mysqldb 记录

---

vim setup_pofix.py #修改mysql_config路径

```
ln -s /usr/local/mysql/lib/libmysqlclient.so.18 /usr/lib
ldconfig
python setup.py install
报错“_mysql.c:29:20: 错误：Python.h：没有那个文件或目录”

yum install python-devel
```

