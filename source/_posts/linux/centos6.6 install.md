---
title: centos6.6 install
categories: []
date: 2015-08-10 17:31:15
---

```
#!/bin/bash
rpm -Uvh  http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
rpm --import http://elrepo.org/RPM-GPG-KEY-elrepo.org
rpm -Uvh http://www.elrepo.org/elrepo-release-6-6.el6.elrepo.noarch.rpm 
#yum update -y
yum install kernel-devel  vim gcc cmake  -y

#openresty(nginx)
mkdir /data/src -p
cd /data/src/
wget http://openresty.org/download/ngx_openresty-1.5.12.1.tar.gz
tar zxvf  ngx_openresty-1.5.12.1.tar.gz
cd ngx_openresty-1.5.12.1
yum install readline-devel pcre-devel openssl-devel -y
yum install gcc gcc-c++ -y
./configure --with-luajit -j2
make -j2
make install
groupadd www
useradd www -g www -M -s /sbin/nologin
mv /usr/local/openresty/nginx/conf/nginx.conf /usr/local/openresty/nginx/conf/nginx.conf.bak
cd /usr/local/openresty/nginx/conf/
wget  http://1.myconf.sinaapp.com/linux_install/nginx.conf.txt   -O  /usr/local/openresty/nginx/conf/nginx.conf
wget http://1.myconf.sinaapp.com/linux_install/fastcgi_parms.txt -O  /usr/local/openresty/nginx/conf/fastcgi_params

mkdir vhosts
cd vhosts
wget http://1.myconf.sinaapp.com/linux_install/default.conf.txt -O /usr/local/openresty/nginx/conf/vhosts/default.conf
wget http://1.myconf.sinaapp.com/linux_install/nginX.txt -O /etc/init.d/nginx
chmod u+x /etc/init.d/nginx

#mysql 
cd /data/src
wget http://1.myconf.sinaapp.com/linux_install/mysql-5.5.37.tar.gz
tar zxvf mysql-5.5.37.tar.gz
cd mysql-5.5.37
cmake  -DCMAKE_BUILD_TYPE:STRING=Release -DMYSQL_USER=mysql -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/data/mysql -DSYSCONFDIR=/etc -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_MEMORY_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DWITH_READLINE=1 -DMYSQL_UNIX_ADDR=/data/mysql/mysql.sock -DMYSQL_TCP_PORT=3306 -DENABLED_LOCAL_INFILE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DEXTRA_CHARSETS=all -DWITH_SSL=yes -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci
make &amp;&amp; make install
mv /etc/my.cnf /etc/my.cnf.bak
cp support-files/my-medium.cnf /etc/my.cnf
wget http://1.myconf.sinaapp.com/linux_install/my.cnf.txt -O /etc/my.cnf
cp support-files/mysql.server /etc/init.d/mysqld
chmod 755 /etc/init.d/mysqld
chkconfig --add mysqld
chkconfig mysqld on
groupadd mysql
useradd mysql -g mysql -M -s /sbin/nologin
mkdir /data/mysql
chown mysql:mysql /data/mysql
cd /usr/local/mysql
./scripts/mysql_install_db --user=mysql  --datadir=/data/mysql
service mysqld restart
chkconfig --add mysqld
chkconfig mysqld on

#php 
cd /data/src
wget http://cn2.php.net/distributions/php-5.6.7.tar.gz
tar zxvf php-5.6.7.tar.gz
cd php-5.6.7
yum install libxml2-devel libcurl-devel libjpeg-devel libpng-devel freetype-devel openldap-devel libmcrypt-devel -y
cp -frp /usr/lib64/libldap* /usr/lib
ln -s /usr/local/mysql/lib/libmysqlclient.so.18 /usr/lib
ldconfig
./configure --enable-fpm --enable-cgi --prefix=/usr/local/php --with-mysql=/usr/local/mysql/ --with-iconv-dir=/usr/local --with-freetype-dir --with-jpeg-dir=/usr/lib --with-png-dir=/usr/lib --with-zlib --with-libxml-dir=/usr --enable-xml --disable-rpath --enable-bcmath --enable-shmop --enable-sysvsem --with-curl --enable-mbregex --enable-mbstring --with-gd --enable-gd-native-ttf --with-openssl --with-mhash --enable-pcntl --with-ldap --with-ldap-sasl --with-xmlrpc --enable-zip --enable-soap --with-mysqli=/usr/local/mysql/bin/mysql_config --with-pdo-mysql=/usr/local/mysql
make
make install
cp php.ini-production /usr/local/php/lib/php.ini -f
cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm  -f
chmod u+x /etc/init.d/php-fpm 
cd /usr/local/php/etc/
cp php-fpm.conf.default php-fpm.conf   -f
sed -i  's/nobody/www/g'  /usr/local/php/etc/php-fpm.conf
mkdir /data/www
echo "&lt;?php phpinfo();" &gt;/data/www/index.php

cd /data/src/
#wget http://downloads.zend.com/guard/7.0.0/zend-loader-php5.6-linux-x86_64.tar.gz

/etc/init.d/nginx restart
/etc/init.d/php-fpm restart
```
