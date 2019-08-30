---
title: lsof/nmap/netstat
categories:
  - linux
tags: [netstat,lsof]
date: 2015-12-02 21:52:07
---

```
lsof -i :22    # 显示22端口当前运行的程序

lsof -c ssh  # 显示ssh进程打开的文件

lsof -p 2120  #显示进程id2120打开的文件


nmap -sP  192.168.1.0/24  #查看192.168.1.0/24有几个用户连接至本机


netstat -tunl #显示正在监听的网络服务端口

netstat -tun #显示已连接的网络服务端口

lsusb  # 显示usb设备
lspic  # 显示pci设备

tar zxvf  abc.tar.gz -C /your/path  #解压文件到指定目录
```
