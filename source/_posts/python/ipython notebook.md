---
title: ipython notebook
categories: [python]
date: 2015-10-28 20:59:09
---

本内容基于centos
yum install sqlite-devel -y # 
安装python2.7.10
谨记--prefix=/usr/local/python27 以及 make altinstall

安装 pip

修改源 vim ~/.pip/pip.conf

```ini
[global]
index-url = http://pypi.douban.com/simple
trusted-host = pypi.douban.com
```

开始
```shell
pip install virtualenv
virtualenv notebook
source notebook/bin/activate
pip install ipython
pip install pyzmq
pip install tornado
pip install MathJax
```
ipython notebook
如出现 ImportError: No module named notebook.notebookapp

```
pip install jupyter
```

出现 ImportError: No module named _sqlite3
```
yum install sqlite-devel -y
```
如果python已经安装好 需要重新编译python

