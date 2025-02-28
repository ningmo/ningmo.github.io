---
title: useful-linux-cmd
date: 2016-10-19 23:05:27
tags: [find,ssh,grep]
categories: linux
---

###### 寻找

```
find .  -mtime -4（4天内）/4(4天前的那一天)/+4（5天前）
locate fielname* (updatedb)
```

###### 寻找定位

```
find ./ -name "*.c" -exec grep -n "string" {} \;
```

###### 寻找与替换

```
find -name "*" -exec sed -i 's/str1/str2/g' {}\;-print
```

###### ssh请求

```
ssh -nNTf  -D 7000 root@IP
ssh -qTfnN -D 7070 root@IP
```
