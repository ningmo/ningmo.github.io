---
title: centos6.7 install chrome
categories: [linux]
tags: [centos]
date: 2015-09-14 14:49:04
---

1.yum仓库 （如果用rpm包安装 可以忽略此步）
```
vim /etc/yum.repos.d/google-chrome.repo
[google-chrome]
name=google-chrome
baseurl=http://dl.google.com/linux/chrome/rpm/stable/$basearch
enabled=1
gpgcheck=1
gpgkey=https://dl-ssl.google.com/linux/linux_signing_key.pub
```
2.安装依赖(当遇到listdc++依赖时)
```
wget http://li.nux.ro/download/nux/dextop/el6/x86_64/nux-dextop-release-0-2.el6.nux.noarch.rpm
rpm -ivh nux-dextop-release-0-2.el6.nux.noarch.rpm
```
3. ```yum install google-chrome-stable```
如果遇到网络失败 百度下载google-chrome当rpm包
下载完成后 点击安装即可

