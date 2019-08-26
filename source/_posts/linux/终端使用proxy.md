---
date: 2016-05-16T23:01:35
title: 终端使用proxy
categories: linux
---

最简单的方法是 执行
```
export http_proxy=http://127.0.0.1:8087
export https_proxy=http://127.0.0.1:8087

```
(以上将http https协议代理到本地的8087端口 且只对当前终端窗口有效)
支持写法 ```http_proxy=http://userName:password@proxyAddress:port```