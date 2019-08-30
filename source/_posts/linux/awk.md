---
title: awk
categories: [linux]
tags: awk
date: 2015-08-10 17:34:43
---

引用：http://www.cnblogs.com/ggjucheng/archive/2013/01/13/2858470.html
```
awk [-F ':' ]  '{print $1}'  filename
cat filename | awk [-F ':' ]  '{print $1}'
[root@local yang]# tail  /etc/passwd |awk -F ':' 'BRGIN {print "name,shell"} {print $1"---"$7} END {print "nameEND,shellEND"}'
ntp---/sbin/nologin
apache---/sbin/nologin
saslauth---/sbin/nologin
postfix---/sbin/nologin
pulse---/sbin/nologin
nameEND,shellEND
搜索并打印所在行
[root@local yang]# awk -F: '/root/' /etc/passwd    #红色部分为正则
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin

$0变量是指整条记录。$1表示当前行的第一个域,$2表示当前行的第二个域,......以此类推

搜索并打印所在行（过滤输出）
[root@local yang]# awk -F: '/root/{print $1}' /etc/passwd
root
operator
内置变量
ARGC               命令行参数个数
ARGV               命令行参数排列
ENVIRON            支持队列中系统环境变量的使用
FILENAME           awk浏览的文件名（输入是文件的时候才会有文件名 如果是搜索别的命令的输出 则不会有文件名）
FNR                浏览文件的记录数
FS                 设置输入域分隔符，等价于命令行 -F选项
NF                 浏览记录的域的个数（列）
NR                 已读的记录数（行）
OFS                输出域分隔符
ORS                输出记录分隔符
RS                 控制记录分隔符
变量输出示例
[root@local yang]# awk -F: '{print "filename:"FILENAME "  no:"NR "  cols:"NF}'  /etc/passwd
filename:/etc/passwd  no:1  cols:7
filename:/etc/passwd  no:2  cols:7
......
使用printf
[root@local yang]# awk -F: '{printf("filename:%s no:%s cols:%s %s\n",FILENAME,NR,NF,$0)}'  /etc/passwd
filename:/etc/passwd no:1 cols:7 root:x:0:0:root:/root:/bin/bash
filename:/etc/passwd no:2 cols:7 bin:x:1:1:bin:/bin:/sbin/nologin
filename:/etc/passwd no:3 cols:7 daemon:x:2:2:daemon:/sbin:/sbin/nologin
filename:/etc/passwd no:4 cols:7 adm:x:3:4:adm:/var/adm:/sbin/nologin
filename:/etc/passwd no:5 cols:7 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
filename:/etc/passwd no:6 cols:7 sync:x:5:0:sync:/sbin:/bin/sync
filename:/etc/passwd no:7 cols:7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
......
awk编程

变量示例
[root@local yang]# awk -F: '{cnt++;print cnt,$1}END{print"cnt:"cnt}'  /etc/passwd
1 root
2 bin
3 daemon
7 shutdown
......
......
35 www
36 mysql
cnt:36
统计文件夹下文件占用字节数(不含子文件夹)
[root@local yang]# ll| awk 'BEGIN {size=0}{size=size+$5}END{print"total size:"size}'
total size:126062
以M计算
[root@local yang]# ll| awk 'BEGIN {size=0}{size=size+$5}END{print"total size:"size/1024/1024"M"}'
total size:126062
循环
[root@local node_modules]# awk -F ':' 'BEGIN {count=1;} {name[count] = $1;count++;}; END{for (i = 1; i &lt; NR+1; i++) print i, name[i]}' /etc/passwd
1 root
2 bin
......
awk分割大文件并
 awk 'NR%12==1{close(p".txt");++p}{print > p".txt"}'    filename 

```