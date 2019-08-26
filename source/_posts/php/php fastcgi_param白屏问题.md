---
title: php fastcgi_param白屏问题
categories:
  - linux
date: 2015-10-31 11:08:54
---

访问php白屏(base on lnmp)
vim nginx/conf/fastcgi_param
```
fastcgi_param  REDIRECT_STATUS    200;
fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
```

