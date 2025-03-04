---
title: apt-get 命令详解
categories: [linux]
tags: [apt-get]
date: 2013-03-25 22:35:11
---



一，什么的是apt-get

高级包装工具（英语：Advanced Packaging Tools,简称：APT）是Debian及其衍生发行版（如：ubuntu）的软件包管理器。APT可以自动下载，配置，安装二进制或者源代码格式的软件包，因此简化了 Unix系统上管理软件的过程,apt-get命令一般需要root权限执行，所以一般跟着sudo命令。它根yum ,pacman,installpkg等性质差不多，是系统软件的管理工具。会用其中一种基本上都会用了。

二，apt-get中文参数

```
    用法：apt-get [选项] 命令  
     apt-get [选项] install|remove pkg1 [pkg2 ...]  
     apt-get [选项] source pkg1 [pkg2 ...]  
```

      
    apt-get 是一个下载安装软件包的简单命令行接口。  
    最常用的命令是update(更新)  
    和install(安装)。  
      
```
   命令：  
     update - 重新获取软件包列表  
     upgrade - 进行更新  
     install - 安装新的软件包  
     remove - 移除软件包  
     autoremove - 自动移除全部不使用的软件包  
     purge - 移除软件包和配置文件  
     source - 下载源码档案  
     build-dep - 为源码包配置编译依赖  
     dist-upgrade - 发行版升级, 参见 apt-get(8)  
     dselect-upgrade - 依照 dselect 的选择更新  
     clean - 清除下载的归档文件  
     autoclean - 清除旧的的已下载的归档文件  
     check - 检验是否有损坏的依赖  
      
    选项：  
     -h 本帮助文件。  
     -q 输出到日志 - 无进展指示  
     -qq 不输出信息，错误除外  
     -d 仅下载 - 不安装或解压归档文件  
     -s 不实际安装。模拟执行命令  
     -y 假定对所有的询问选是，不提示  
     -f 尝试修正系统依赖损坏处  
     -m 如果归档无法定位，尝试继续  
     -u 同时显示更新软件包的列表  
     -b 获取源码包后编译  
     -V 显示详细的版本号  
     -c=? 阅读此配置文件  
     -o=? 设置自定的配置选项，如 -o dir::cache=/tmp  

三，apt-get 常用实例

apt-cache search packagename 搜索包
apt-cache show packagename 获取包的相关信息，如说明、大小、版本等
apt-get install packagename 安装包
apt-get install packagename - - reinstall 重新安装包
apt-get -f install 修复安装"-f = --fix-missing"
apt-get remove packagename 删除包
apt-get remove packagename - - purge 删除包，包括删除配置文件等
apt-get update 更新源
apt-get upgrade 更新已安装的包
apt-get dist-upgrade 升级系统
apt-get dselect-upgrade 使用 dselect 升级
apt-cache depends packagename 了解使用依赖
apt-cache rdepends packagename 是查看该包被哪些包依赖
apt-get build-dep packagename 安装相关的编译环境
apt-get source packagename 下载该包的源代码
apt-get clean 清理无用的包
apt-get autoclean 清理无用的包
apt-get check 检查是否有损坏的依赖

```
地址:http://blog.51yip.com/linux/1176.html	
