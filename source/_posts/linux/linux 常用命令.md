---
title: linux 常用命令
categories:
  - linux
date: 2015-10-31 10:59:06
---

常用命令
```
wc -l `find . -name "*.css"`|tail -n1
```
指定目录下 在指定后缀文件 查找关键字
```
find ./ -name "*" -exec grep "string" {} \;
```

