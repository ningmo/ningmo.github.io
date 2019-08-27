---
title: docker常用命令操作
date: 2016-11-02 21:31:10
tags: [docker,run]
categories: Linux
---

##### docker 常用命令

```
## 拉取镜像
docker pull openresty/openresty
docker images

# 创建容器
docker run -it -m 100MB -h doc.nginx -d --name="nginx" openresty/openresty 
# -c cpu优先级  －m 内存（M） －h 主机  --name 容器名字  镜像  ［/bin/bash 进入容器命令行］
# -p  端口映射 主机端口:容器端口  -v 主机目录:容器目录

示例：
docker run -itd --name="cent" -m 512M -p 8080:80 -p 9000:9000 -v /Users/moran/Documents/angular/:/data/angular centos

＃ 常用操作
docker inspect 容器名字 ＃ 查看容器的信息
docker attach 	# 连接(进入)到一个正在运行的容器
docker creeate 	# 创建但不运行
docker run		# 创建并运行
docker top 		# 显示容器的进程信息
docker cp 		# 从容器里向外拷贝文件或目录
docker logs 	# 查看容器的日志 !! 可以看到其标准（错误）输出
docker exec -it 容器名字 bash		# 在容器中创建一个新的bash会话

docker ps -[l|a]
docker start|stop|restart|pause|unpause 容器名字/ID
docker rm 容器
docker rmi 镜像


docker build 	# 使用Dockerfile创建镜像
docker history 	# 显示生成一个镜像的历史命令


示例： 在官方centos7镜像上 安装一个node环境（虽然node也有镜像 但更喜欢自己装）
docker pull centos
docker run -itd --name="cent" -m 512M -p 8080:80 -p 9000:9000 -v /Users/moran/Documents/angular/:/data/angular centos
docker attach cent

#  基础工具
yum install wget vim gcc-c++ make -y
yum install gem -y 
gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/
gem install compass

#  install npm
cd /tmp
wget https://nodejs.org/dist/v6.9.1/node-v6.9.1.tar.gz
tar zxvf node-v6.9.1.tar.gz 
cd node-v6.9.1
./configure 
make
make install 

# tools
npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm install -g grunt-cli 
cnpm install -g yo
cnpm install -g bower
cnpm install -g generator-karma
cnpm install -g generator-angular



# 保存容器
docker commit 版本ID 远程仓库




```
