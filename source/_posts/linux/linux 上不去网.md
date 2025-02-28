---
title: linux 上不去网
categories:
  - linux
date: 2015-12-02 11:03:00
---

linux 上不去网   ip dns无误 ping可以到达网关

可能原因 网卡睡眠

ethtool eht0  //查看eht0网口基本设置

mii-tool -w eth0
