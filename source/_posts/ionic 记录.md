---
title: ionic 记录
categories: []
date: 2015-11-05 17:33:35
---

```
#rpm -Uvh  http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
#rpm --import http://elrepo.org/RPM-GPG-KEY-elrepo.org
#rpm -Uvh http://www.elrepo.org/elrepo-release-6-6.el6.elrepo.noarch.rpm 

yum install npm -y # 需要epel源
npm install cordova ionic 

mkdir try && cd $_
ionic start myApp tabs #新建项目 类型 tabs 头尾  sidemenu 左右  blank 空白项目

#添加到系统平台
cd myApp
ionic platform add android
ionic build android
ionic emulate android

ionic serve


# 官网： http://ionicframework.com/getting-started/
```
