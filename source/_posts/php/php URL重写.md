---
title: php URL重写
categories: []
date: 2012-12-18 23:19:30
---

话说重写：
网站结构如下：
index.php
source/src_{模块名}.php
template/tpl_{模块名}.php
通过首页统一入口来访问各个模块文件，
比如，最简单的统一入口访问代码：
define('ROOT', dirname(__FILE__));
$mod = isset($_GET['mod']) ? $_GET['mod'] : 'index';
$src = ROOT . 'source/src_' . $mod . '.php' ;
$tpl = ROOT . 'template/src_' . $mod . '.php' ;
file_exists($src) &amp;&amp; include($src) ;
file_exists($tpl) &amp;&amp; include($tpl) ;
现在访问一个地址：index.php?mod=news ；则会访问source/src_news.php 文件。
现在要让url重写，访问 /news 这个url能实现一样的效果,
Linux下的Apache和Nginx，
windows下的ISAPI_Rewrite模块重写，
重写要求，如果我访问的是不存在的文件，重定向到index.php，通过获取实际访问的url，来判断该调用哪个文件，如果我访问 /news?page=1 这个URL，重定向后：
三个服务器访问后的结果：
Apache 关于开启rewrite模块后，会产生三个头部：
$_SERVER['REDIRECT_STATUS'] =&gt; 200 （证明重写成功）
$_SERVER['REDIRECT_QUERY_STRING'] =&gt; page=1
$_SERVER['REDIRECT_URL'] =&gt; /news
而IIS的rewrite模块重写后，会有
$_SERVER['REDIRECT_STATUS'] =&gt; 200
$_SERVER['HTTP_X_REWRITE_URL'] =&gt; /news
Nginx重写后有：
$_SERVER['REDIRECT_STATUS'] =&gt; 200
所以，如果采用 $_SERVER['REDIRECT_URL'] 或 $_SERVER['HTTP_X_REWRITE_URL']都是不可取的，因为Nginx重写后，不产生重写URL，三个服务器并没有交集，如果还考虑其他服务器比如 lighttpd，cheroke等，可能产生的头部更加不一致。
通常需要考虑的就是PHP的 $_SERVER['REQUEST_URI']
现在看看这几个服务器产生的 REQUEST_URI是否一致：
apache下：
$_SERVER['REQUEST_URI'] =&gt; /news
Nginx下：
$_SERVER['REQUEST_URI'] =&gt; /news
IIS下：
$_SERVER['REQUEST_URI'] =&gt; /index.php/news
这他妈是怎么回事？干嘛加个/index.php这个玩意呢？我没有搞懂，不过IIS的重写不完全支持.htaccess语法，所以，不知道是否是我配置的问题，但是我绞尽脑汁也弄不掉这个/index.php
所以，干脆用IIS专用的 $_SERVER['HTTP_X_REWRITE_URL'] 来替换 $_SERVER['REQUEST_URI']
暂时世界清静了。
要提取完整的重写后的URL地址，还没这么简单，需要考虑子目录下重写的情况，下回放出完整的无bug的提取重写url的函数，依靠此函数，将函数分割，然后形成$controller, $action .. 等参数
