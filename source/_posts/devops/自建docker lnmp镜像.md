---
title: 自建docker lnmp镜像
tags: [linux,docker,centos,lnmp]
categories: [linux,docker]
date: 2016-06-05 11:59:28
---
```linux
docker pull centos ＃ 拉取镜像到本地
docker run  -i -t -p 8000:80  --name=centosDev centos  
cat /etc/redhat-release #查看系统版本
>CentOS Linux release 7.2.1511 (Core)

＃ 开发环境安装  采取源码安装
yum install kernel-devel  vim gcc cmake  -y
mkdir /data/src -p
cd /data/src/
yum install wget -y

# nginx   安装路径/usr/local/openresty/nginx/  运行用户www
wget http://openresty.org/download/ngx_openresty-1.5.12.1.tar.gz
tar zxvf  ngx_openresty-1.5.12.1.tar.gz
cd ngx_openresty-1.5.12.1
yum install readline-devel pcre-devel openssl-devel -y
yum install gcc gcc-c++ make -y
./configure --with-luajit -j2
make -j2
make install
groupadd www
useradd www -g www -M -s /sbin/nologin
cd /usr/local/openresty/nginx/conf/
mv nginx.conf nginx.conf.bak
vi /usr/local/openresty/nginx/conf/nginx.conf




user  www;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

pid        logs/nginx.pid;

events {
    use epoll;
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;
    charset utf-8;
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;
    access_log off;
    server_names_hash_bucket_size 128;
    client_header_buffer_size 128k;
    large_client_header_buffers 4 128k;
    client_max_body_size 300m;


    sendfile        on;
    server_tokens off;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    tcp_nodelay on;

    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;

    #add_header Vary Accept-Encoding;
    include vhosts/*.conf;

}

cd /usr/local/openresty/nginx/conf/
mkdir vhosts
vim vhosts/default.conf


server {
        listen   80;

        root /data/www;
        index index.html index.htm;

        server_name localhost;

        location / {
                try_files $uri $uri/ /index.html;
                # include /etc/nginx/naxsi.rules
        }
        location ~ .*\.php?$
        {
            include fastcgi_params;
        }
}


vim fastcgi_params

if ($request_filename ~* (.*)\.php) {
    set $php_url $1;
}
if (!-e $php_url.php) {
    return 403;
}

fastcgi_ignore_client_abort  on;
fastcgi_pass   127.0.0.1:9000;
fastcgi_index  index.php;


# mysql
cd /data/src/
wget http://120.52.72.20/cdn.mysql.com/c3pr90ntc0td//Downloads/MySQL-5.7/mysql-5.7.13.tar.gz
tar zxvf mysql-5.7.13.tar.gz
cd mysql-5.7.13
cmake  -DCMAKE_BUILD_TYPE:STRING=Release -DMYSQL_USER=mysql -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/data/mysql -DSYSCONFDIR=/etc -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_MEMORY_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DWITH_READLINE=1 -DMYSQL_UNIX_ADDR=/data/mysql/mysql.sock -DMYSQL_TCP_PORT=3306 -DENABLED_LOCAL_INFILE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DEXTRA_CHARSETS=all -DWITH_SSL=yes -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/usr/local/boost
make && make install  ＃ 30分钟...
cp support-files/mysql.server /etc/init.d/mysqld
chmod 755 /etc/init.d/mysqld
groupadd mysql
useradd mysql -g mysql -M -s /sbin/nologin
mkdir /data/mysql
chown mysql:mysql /data/mysql
cd /usr/local/mysql
cp support-files/my-default.cnf /etc/my.cnf
/usr/local/mysql/bin/mysqld --initialize –user=mysql
/etc/init.d/mysqld start

# 线上镜像 docker pull ningmo/centos_lnmp:v1

# php
wget http://cn2.php.net/get/php-7.0.7.tar.gz/from/this/mirror
tar zxvf mirror 
cd php-7.0.7
yum install libxml2-devel libcurl-devel libjpeg-devel libpng-devel freetype-devel openldap-devel libmcrypt-devel -y
cp -frp /usr/lib64/libldap* /usr/lib
ln -s /usr/local/mysql/lib/libmysqlclient.so.18 /usr/lib
ldconfig
./configure --enable-fpm --enable-cgi --prefix=/usr/local/php --with-mysql=/usr/local/mysql/ --with-iconv-dir=/usr/local --with-freetype-dir --with-jpeg-dir=/usr/lib --with-png-dir=/usr/lib --with-zlib --with-libxml-dir=/usr --enable-xml --disable-rpath --enable-bcmath --enable-shmop --enable-sysvsem --with-curl --enable-mbregex --enable-mbstring --with-gd --enable-gd-native-ttf --with-openssl --with-mhash --enable-pcntl --with-ldap --with-ldap-sasl --with-xmlrpc --enable-zip --enable-soap --with-mysqli=/usr/local/mysql/bin/mysql_config --with-pdo-mysql=/usr/local/mysql --enable-opcache=no
make
make install
cp php.ini-production /usr/local/php/lib/php.ini -f
cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm  -f
chmod u+x /etc/init.d/php-fpm 
cd /usr/local/php/etc/
cp php-fpm.conf.default php-fpm.conf   -f
sed -i  's/nobody/www/g'  /usr/local/php/etc/php-fpm.conf
mkdir /data/www
echo "<?php phpinfo();" >/data/www/index.php


```


