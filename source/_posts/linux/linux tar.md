---
title: linux tar
categories:
  - linux
  - tar
date: 2015-10-31 10:49:30
---

解压
```
.tar.bz                 tar zxvf  file.tar.gz
.tar.gz2                tar jxvf file.tar.bz2
.bz                     gzip -d  file.bz
.gz2                    bzip2 -d file.gz2
```

压缩
```
.gz                     gzip    -v    file
.bz2                    bzip2    -v    file
```

```
.tar.gz                tar -zcvf file.tar.gz  dir
.tar.bz2               tar -jcvf file.tar.gz2  dir
```


查看 
```
zcat
bzcat
```

