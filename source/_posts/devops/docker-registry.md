---
title: 修改docker源地址
date: 2016-11-10 21:20:08
tags: docker
categories: docker
---


mac 直接找到配置 在registry加入地址即可
http://b72d1176.m.daocloud.io

其他系统 修改docker配置文件/etc/default/docker如

DOCKER_OPTS="--registry-mirror=http://b72d1176.m.daocloud.io"

b72d1176为个人帐号的生成

可以自己注册帐号后在加速器中找到自己的URL
感谢 [daocloud](https://www.daocloud.io)