---
date: 2015-11-06T20:17:37

title: centos emacs安装

---

wget http://mirrors.ustc.edu.cn/gnu/emacs/emacs-24.5.tar.gz
tar zxvf emacs-24.5.tar.gz
cd emacs-24.5
yum install libXt-devel libext-devel readline-devel  libXaw-devel -y
./configure --prefix=/usr/local/emacs
报错
```
configure: error: The following required libraries were not found:
     libgif/libungif libtiff
Maybe some development libraries/packages are missing?
If you don't want to link with them give
     --with-gif=no --with-tiff=no
as options to configure

yum install libungiflibgif-devel libtifflibgif-devel -y
make && make install
```