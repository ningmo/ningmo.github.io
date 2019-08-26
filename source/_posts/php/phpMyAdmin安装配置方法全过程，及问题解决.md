---
title: phpMyAdmin安装配置方法全过程，及问题解决
categories: []
date: 2013-03-30 12:04:13
---

1、先下载phpMyAdmin安装包：
到国外官方网站<a href="http://www.phpmyadmin.net/" target="_blank">http://www.phpmyadmin.net</a>上，点击导航栏上的“DOWNLOADS”链接进入下载页面，选择“all-languages.zip”版本下载到本地，解压；把解压缩文件放到系统指定的虚拟根目录下的phpmyadmin文件夹里（可自定义文件夹名），如flymorn的本地存放位置是D:\www\phpmyadmin里。

&nbsp;

2、找到phpmyadmin文件夹里的/libraries/config.default.php文件(旧版本是根目录下的config.inc.php文件)，用支持UTF-8编码的写字板打开进行编辑。

&nbsp;

3、查找 $cfg['PmaAbsoluteUri']
修改为你将上传到空间的phpMyAdmin的网址
如：$cfg['PmaAbsoluteUri'] = 'http://www.piaoyi.org/phpmyadmin/';
这里因为我是本地调试，我改为$cfg['PmaAbsoluteUri'] = 'http://localhost/phpmyadmin/';
注意：不要漏掉最后的反斜杠/和开头的http。

&nbsp;

4、查找 $cfg['Servers'][$i]['host'] = 'localhost';（通常用默认，也有例外，可以不用修改）

&nbsp;

5、查找 $cfg['Servers'][$i]['auth_type'] = 'config';
在自己的机子里调试用config；如果在网络上的空间用cookie，这里我们既然在前面已经添加了网址，就修改成cookie。
我个人建议：无论是本地还是网络上，都建议设置成cookie，安全第一。
同时，当auth_type这个值设置为cookie后，还有一个地方需要作出相应的修改：
$cfg['blowfish_secret'] = '';
改成：$cfg['blowfish_secret'] = 'www.piaoyi.org';
这里的www.piaoyi.org自己随便定义，不超过46个字符。如果这个地方留空，将会出现上文提到的第2个错误：“配置文件现在需要绝密的短语密码(blowfish_secret)”。

&nbsp;

6、查找 $cfg['Servers'][$i]['user'] = 'root'; // MySQL user（用户名，自己机里用root；在网上一般为你的ftp用户名，虚拟主机提供商会告诉你的；一般不要修改）

&nbsp;

7、查找 $cfg['Servers'][$i]['password'] = '123456'; // MySQL password （123456修改成连接你的MYSQL数据库的用户密码）

&nbsp;

8、查找 $cfg['DefaultLang'] = 'zh'; （这里是选择语言，zh代表简体中文的意思） 还有 $cfg['DefaultCharset'] = 'gb2312';（修改默认编码为国标）

&nbsp;

到这里，phpMyAdmin就已经配置完成OK；你可以打开http://localhost/phpmyadmin/访问方便快捷的图形化管理软件phpMyAdmin了。关于本文没有提到的配置文件里的其他选项，大家可以不用关心了也不必修改。关于具体如何操作phpMyAdmin不在本文讨论的范围内，不过图形化界面，是通俗易懂，稍微看看就会用了（<a href="http://www.jacro.cn/archives/2007/20858.html" target="_blank">参考</a>）。

&nbsp;

下面谈谈，flymorn在安装配置phpMyAdmin的过程中所遇到的几个问题。

&nbsp;

<strong>第一个问题：“无法载入mcrypt扩展，请检查PHP配置”。</strong>

&nbsp;

1、没有正确安装Mysql数据库，在系统服务中Mysql相关的服务没有启动。
2、在系统的 system32（C:\windows\system32） 目录下缺少 libmcrypt.dll文件，解决方法是找到php目录下的libmcrypt.dll，并将libmcrypt.dll复制到C:\windows\system32目录中，然后重新启动Web服务。
3、在PHP目录下的php.ini文件中，没有将“;extension=php_mcrypt.dll”中的前面一个“;”去掉，所以不能使用相应功能，解决方法是打开php.ini文件，找到;extension=php_mcrypt.dll改成extension=php_mcrypt.dll     //去掉前面的;使之生效
4、Mysql目录没有读取权限，正确的目录权限如下：
administrator    完全控制
system     完全控制
user     读取加运行
其他的用户权限全部删除（也可保留，但安全性不高，建议删除），然后重启MYsql服务和Web服务（建议修改此项后重启一下服务器）。
5、以上方法都不行的话，再用这个方法：桌面&gt;我的电脑&gt;右键属性&gt;高级&gt;环境变量&gt;系统变量&gt;新建
名：phpdir
值：C:\php(你的PHP在什么目录就是什么)
（参考来源：<a href="http://www.phperz.com/html/PHPjichubiancheng/xinshouzhuanqu/20080103/587.html" target="_blank">地址</a>）

&nbsp;

<strong>第二个问题：“无法载入 mysql 扩展，请检查 PHP 配置”</strong>

&nbsp;

把 PHP 目录下的 libmySQL.dll 复制到:c:\windows\system32 下，然后重启APACHE。

&nbsp;

<strong>第三个问题：“配置文件现在需要绝密的短语密码(blowfish_secret)”</strong>

&nbsp;

确保在config.default.php文件里面进行了正确的设置 ：
$cfg['Servers'][$i]['auth_type']= 'cookie';
同时
$cfg['blowfish_secret'] = 'www.piaoyi.org'; //（这个值随便写，就是不能留空）

&nbsp;

其实这个问题，我碰到了确实很郁闷，如果按照我在上面提到的配置方法做了，应该不会提示这个错误。经过我2个多小时不停的修改config.default.php配置文件，始终提示这个错误，几乎达到了崩溃的边缘了。

&nbsp;

然后就是灵光一现，突然发现：我把auth_type认证方式改为http，改为config时，打开页面始终出现这个错误，也就是说改变了认证模式，并没有对phpmyadmin起作用，难道是其他的配置文件在影响决定着phpmyadmin？

&nbsp;

立马来到phpmyadmin根目录，赫然发现根目录下有一个config.sample.inc.php文件存在的同时，还有一个文件config.inc.php也存在！！而这个是以前老版本的配置文件，原来是这个文件捣的鬼。马上删除config.inc.php，再进入调试页面，错误消失了！问题解决。

&nbsp;

看来phpmyadmin根目录下的config.inc.php配置文件的优先级比/libraries/config.default.php的配置文件高，系统是先调用根目录里的配置文件，然后才是libraries文件夹里的配置文件，艾，这个问题，折腾了这么久，呜呼哀哉。总归解决了，舒一口气啊。

&nbsp;

<strong>第四个问题：“#2003-服务器没有响应”。</strong>

&nbsp;

遇到这个问题多半是MYSQL数据库没有启动，建议直接在在：控制面板-管理工具-服务里面把MYSQL启动。关于是否启动，你可以在任务管理器里查看是否有“mysqld-nt.exe”这样的进程，如有有，则说明mysql已经启动。

<strong>第五个问题：“没有发现PHP的扩展设置mbstring”。</strong>

&nbsp;

修改 C:\WINDOWS\ 下的 PHP.INI 文件，找到 ;extension=php_mbstring.dll 把;去掉，保存 PHP.INI，重新启动Apache即可。

&nbsp;

这些问题，不碰到便罢了。但是对于新手来说，第一次碰到的话，自己查找解决方案的话一般都是到处查找，网上一大堆的方法都不适用的情况下，自己会感觉到多么的无奈了。我还是建议，遇到问题不要着急，仔细阅读文档，多参考下前辈们的解决方法，虽然不一定适用，但对你会有一定的启发。多动手，多试验，没有任何问题可以难倒你。

&nbsp;

&nbsp;

来源：http://www.piaoyi.org/php/phpMyAdmin-install-method-faqs.html
