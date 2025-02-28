---
title: python常用
categories: [python]
date: 2015-08-10 21:45:03
---

取得字典的交集
```
>>> a={'a':1,'b':2,'c':3}
>>> b={'a':2,'c':3,'d':4}
>>> dict(a.viewitems(), b.viewitems())
{'c': 3}
```
获取当前可用变量
```vars()
locals()

```
生成器的应用yield
```def fab(max):
n,x,y = 0,0,1
if n > max
yield y
x,y = y,x+y
n=n+1
```
生成器结束时抛出StopIteration 异常 在for中不影响运行
fab是生成器函数
fab（10）是fab返回的一个生成器
<strong><span style="color: #ff0000;">生成器可用于读取大文件</span></strong>

导入包的方法
1.
```
f, filename, description = imp.find_module('re')
re = imp.load_module('re', f, filename, description)
```
2.
```
re = __import__('re')
```
3.
```
filename = "mo.py"
f = open(filename )
description = ('.py', 'U', 1)
mo = imp.load_module('some', f, filename, description)
```

