---
title: openwrt chinadns
categories: [openwrt]
date: 2015-11-22 00:05:09
---

```
opkg update
opkg install ip ipset resolveip iptables-mod-tproxy resolveip libopenssl luci-i18n-base-zh-cn
reboot
opkg update   # redo 
wget https://github.com/shadowsocks/ChinaDNS/releases/download/1.3.2/ChinaDNS_1.3.2_ar71xx.ipk
wget http://openwrt-dist.sourceforge.net/releases/ar71xx/packages/shadowsocks-libev-spec_2.4.0-1_ar71xx.ipk
wget http://openwrt-dist.sourceforge.net/releases/luci/packages/luci-app-chinadns_1.3.6-1_all.ipk
wget http://openwrt-dist.sourceforge.net/releases/luci/packages/luci-app-shadowsocks-spec_1.3.6-1_all.ipk

opkg install ChinaDNS_1.3.2_ar71xx.ipk
opkg install shadowsocks-libev-spec_2.4.0-1_ar71xx.ipk
opkg install luci-app-chinadns_1.3.6-1_all.ipk #chinadns的web界面，不过我的安装没有生效。web上没有出现菜单，但不影响使用
opkg install luci-app-shadowsocks-spec_1.3.6-1_all.ipk

/etc/init.d/chinadns enable
/etc/init.d/shadowsocks enable

wget -O- 'http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest' | awk -F\| '/CN\|ipv4/ { printf("%s/%d\n", $4, 32-log($5)/log(2)) }' > /etc/ignore.list

```

vim /etc/config/network
```
config interface 'wan'
        option ifname 'eth0'
        option proto 'dhcp'
        option peerdns '0'
        option dns '8.8.8.8 8.8.4.4 223.5.5.5'
```

详情参考：<a href="http://blog.91python.com/openwrt/shadowsocks/2015/10/15/tp720n-openwrt-shadowscoks.html" target="_blank">http://blog.91python.com/openwrt/shadowsocks/2015/10/15/tp720n-openwrt-shadowscoks.html</a>
