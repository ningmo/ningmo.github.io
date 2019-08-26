---
date: 2013-07-18T16:34:42

title: apache多个域名指定相应的目录

---

apache多个域名指定相应的目录
1. 在C:\WINDOWS\system32\drivers\etc路径下找到hosts，增加内容：
    计算机ip 域名1
    计算机ip 域名2
 
2. 修改apache的配置文件httpd.conf，增加内容如下：
```
NameVirtualHost *:80
<VirtualHost *:80>
       DocumentRoot "D:/wamp/www/"
       ServerName 域名1
       <Directory "D:/wamp/www/">
              AllowOverride all
              Order allow,deny
              allow from all
        </Directory>
</VirtualHost>
 
<VirtualHost *:80>
        DocumentRoot "D:/wamp/www/eight/"
        ServerName 域名2
        <Directory "D:/wamp/www/eight/">
               AllowOverride all
               Order allow,deny
               allow from all
         </Directory>
</VirtualHost>
```

 
3. ①在浏览器中输入：域名1/www下的任意文件
    此时域名1访问的目录是D:/wamp/www/
    ②在浏览器中输入：域名2/eight/下的任意文件
    此时域名1访问的目录是D:/wamp/www/eight/
注：NameVirtualHost *:80 这句必须添加，或者去掉Include conf/extra/httpd-vhosts.conf行首
       的井号，不然会出问题

来自：http://hi.baidu.com/chevallet/item/43c938a6f2a16fd55af191d9
