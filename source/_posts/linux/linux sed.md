---
title: linux sed
categories:
  - linux
date: 2015-10-31 10:46:55
---

原文引用：http://www.cnblogs.com/ggjucheng/archive/2013/01/13/2856901.html

```
[root@www ~]# sed [-nefr] [动作]
选项与参数：
-n ：使用安静(silent)模式。在一般 sed 的用法中，所有来自 STDIN 的数据一般都会被列出到终端上。但如果加上 -n 参数后，则只有经过sed 特殊处理的那一行(或者动作)才会被列出来。
-e ：直接在命令列模式上进行 sed 的动作编辑；
-f ：直接将 sed 的动作写在一个文件内， -f filename 则可以运行 filename 内的 sed 动作；
-r ：sed 的动作支持的是延伸型正规表示法的语法。(默认是基础正规表示法语法)
-i ：直接修改读取的文件内容，而不是输出到终端。
动作说明： [n1[,n2]]function
n1, n2 ：不见得会存在，一般代表『选择进行动作的行数』，举例来说，如果我的动作是需要在 10 到 20 行之间进行的，则『 10,20[动作行为] 』
function：
a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～
c ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！
d ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；
p ：列印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～
s ：取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配正规表示法！例如 1,20s/old/new/g 就是
```
对输出进行行删除
```
[root@local yang]# nl passwd |sed '2,33d'
1	root:x:0:0:root:/root:/bin/bash
34	yang:x:500:500:yang:/home/yang:/bin/bash
35	www:x:501:501::/home/www:/sbin/nologin
36	mysql:x:502:502::/home
```


替换行
```
[root@local yang]# nl passwd |sed '2,5c 1111'
1	root:x:0:0:root:/root:/bin/bash
1111
6	www:x:501:501::/home/www:/sbin/nologin
7	mysql:x:502:502::/home/m
```


列出指定行内容
```
[root@local yang]# nl passwd |sed -n '2,3p'
2	saslauth:x:498:76:"Saslauthd user":/var/empty/saslauth:/sbin/nologin
3	postfix:x:89:89::/var/spool/postfix:/sbin/nologin
```

搜索关键字所在行
```
[root@local yang]# nl passwd |sed -n '/root/p' #p 打印 d 删除.....
1	root:x:0:0:root:/root:/bin/bash
```

搜索并执行命令
```
[root@local yang]# nl passwd |sed -n '/root/{s/root/aaa/;p;q}'  #  :q执行一次就退出
   1	aaa:x:0:0:root:/root:/bin/bash
```

搜索与替换
```
[yang@local ~]$ ifconfig eth0|grep 'inet addr'|sed 's/^.*addr://g' |sed 's/Bcast.*$//g'
192.168.50.168
```

对文件的搜索与替换
```
[root@local yang]# nl passwd
1	root:x:0:0:root:/root:/bin/bash
2	saslauth:x:498:76:"Saslauthd user":/var/empty/saslauth:/---/login
3	postfix:x:89:89::/var/spool/postfix:/---/login

[root@local yang]# sed -n 's/---/sbin/g;p' passwd     #替换预览
root:x:0:0:root:/root:/bin/bash
saslauth:x:498:76:"Saslauthd user":/var/empty/saslauth:/sbin/login
postfix:x:89:89::/var/spool/postfix:/sbin/login

[root@local yang]# sed -i 's/---/sbin/g' passwd    #替换 sed -i 's/---/sbin/g;p' passwd 加p将会将输出追加到文件内容
[root@local yang]# nl passwd
1	root:x:0:0:root:/root:/bin/bash
2	saslauth:x:498:76:"Saslauthd user":/var/empty/saslauth:/sbin/login
3	postfix:x:89:89::/var/spool/postfix:/sbin/login
```

