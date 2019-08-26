---
date: 2016-02-01T11:56:04

title: mac install dev

---

环境开发配置
mysql 
下载mysql压缩包.tar.gz 
解压
mv mysql_5.7.10_osx... /usr/local/mysql
sudo chown -R root:wheel /usr/local/mysql 
/usr/local/mysql/bin/mysqld --initialize --user=mysql
/usr/local/mysql/support-files/mysql.server start/stop/restart/status

/usr/local/mysql/bin/mysqladmin -uroot -p 之前输出的默认密码 password  ＃ 执行后 输入新密码



nginx
brew update 
brew install pcre openssl
tar ....
./configure    --with-cc-opt="-I/usr/local/opt/openssl/include/ -I/usr/local/opt/pcre/include/"    --with-ld-opt="-L/usr/local/opt/openssl/lib/ -L/usr/local/opt/pcre/lib/"
make && make install 


