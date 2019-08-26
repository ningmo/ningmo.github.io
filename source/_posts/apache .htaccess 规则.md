---
date: 2012-12-18T23:18:20
title: apache .htaccess 规则
---

一、什么是.ht<a href="http://www.blags.org/tags/access/">access</a>文件

.htaccess文件是Apache服务器上的一个设置文件。它是一个文本文件，可以使用任何文本编辑器进行编写。.htaccess文件提供了针对目录改变配置的方法，即通过在一个特定的文档目录中放置一个包含一个或多个指令的文件（.htaccess文件），以作用于此目录及其所有子目录。.htaccess的功能包括设置网页密码、设置发生错误时出现的文件、改变首页的文件名（如index.html）、禁止读取文件名、重新导向文件、加上MIME类别、禁止列目录下的文件等。
在需要针对目录改变服务器的配置，而对服务器系统没有root权限时，应该使用.htaccess文件。如果服务器管理员不愿意频繁修改配置，则可以允许用户通过.htaccess文件自己修改配置，尤其是ISP在一台机器上提供多个用户站点，而又希望用户可以自己改变配置的情况下，一般会开放部分.htaccess的功能给使用者自行设置。
注意：.htaccess是一个完整的文件名，不是***.htaccess或其它格式（当然也有管理员把其设置成其它名字，但一般都是使用.htaccess）。另外，上传.htaccess文件时，必须使用ASCII模式，并使用chmod命令改变权限为：644（RW_R__R__）。每一个放置.htaccess的目录和其子目录都会被.htaccess影响。例如，在/abc/目录下放置了一个.htaccess文件，那么/abc/和 /abc/def/内所有的文件都会被它影响，但/index.html不会被它影响，这一点是很重要的。

二、使用.htaccess配置文件
在配置Apache时，除了可以在主配置文件（http.conf）中配置访问控制之外，还可以使用.htaccess 文件配置对指定目录的访问控制。使用.htaccess文件可以改变主配置文件中的配置，但是它只能设置对指定目录的访问控制，这个目录就是.htaccess文件存放的目录。在一个目录下设置了.htaccess文件之后，当用户使用浏览器访问此目录时，Apache会读取该文件的配置来覆盖主配置文件（http.conf）的配置。
注意：
1.修改.htaccess文件无需重新启动Apache服务器，而是立即生效。
2.在可能的情况下应该尽量避免使用.htaccess文件，因为使用.htaccess文件会降低服务器的运行性能。
何时使用.htaccess文件：
有如下两种情况需要使用.htaccess文件：
*在多个用户之间分割配置
*想在不重新启动服务器的情况下改变服务器配置
使用.htaccess文件必须经过两个配置步骤：
*首先在主配置文件中的启用并控制对.htaccess文件的使用
*然后在需要覆盖主配置文件的目录下生成.htaccess文件
在主配置文件中的启用并控制对.htaccess文件的使用
1.设置文件名称
必须保证在主配置文件中包含如下的配置语句：
AccessFileName .htaccess
&lt;Files ~ “^\.htaccess”&gt;
Order allow,deny
Deny from all
&lt;/Files&gt;

2.控制在.htaccess文件中可以使用的指令组
要控制在.htaccess文件中可以使用的指令组，需要在主配置文件中使用AllowOverride指令。下面列出了可以在AllowOverride指令所使用的指令组。
指令组 可用指令 说明
============================
AuthConfig
AuthDBMGroupFile, AuthDBMUserFile, AuthGroupFile, AuthName, AuthType, AuthUserFile, Require
进行认证、授权以及安全的相关指令
=============================
FileInfo
DefaultType, ErrorDocument, ForceType, LanguagePriority, SetHandler, SetInputFilter, SetOutputFilter
控制文件处理方式的相关指令
=============================
Indexes
AddDescription, AddIcon, AddIconByEncoding, AddIconByType, DefaultIcon, DirectoryIndex, FancyIndexing, HeaderName, IndexIgnore, IndexOptions, ReadmeName
控制目录列表方式的相关指令
==============================
Limit
Allow,Deny,Order
进行目录访问控制的相关指令
==============================
Options
Options, XBitHack

启用不能在主配置文件中使用的各种选项

=============================

All

全部指令组

可以使用以上所有指令

=============================

None

禁止使用所有指令

禁止处理.htaccess文件

==============================

3、生成.htaccess文件

当在主配置文件中配置了对.htaccess文件的启用和控制之后，接下来就可以在需要覆盖主配置文件的目录下生成.htaccess文件。.htaccess文件中可以使用的配置指令取决于主配置文件中AllowOverride指令的设置。

4、使用.htaccess文件举例

下面举一个简单的例子说明.htaccess文件的使用。

//首先在文档根目录下生成一个private目录，并创建测试文件

# cd /var/www/html

# mkdir private

# cd private

# touch test

//修改配置前，在客户浏览器查看结果。

//修改主配置文件

# vi /etc/httpd/conf/httpd.conf

//添加如下配置语句

&lt;Directory “/var/www/html/private”&gt;
AllowOverride Options
&lt;/Directory&gt;

#

//重新启动httpd

# service httpd restart

//在/var/www/html/private目录下生成.htaccess文件

# vi /var/www/html/private/.htaccess

//添加如下配置语句

Options –Indexes

#

//在客户浏览器中查看结果。

//即对private目录的访问不生成文件列表

三、.htaccess文件的配置说明

Apache可以使用分布在整个网页结构中的特殊文件来进行配置，这些特殊文件通常叫.htaccess，也可以用AccessFileName指令来改变。.htaccess 文件中的指令的作用域是存放它的那个目录及其所有子目录。.htaccess 文件的语法与主配置文件相同。由于对每次请求都会读取.htaccess 文件，所以对这些文件的改变会立即生效。

可以查阅Context了解可以放在.htaccess文件中的指令；服务器管理员可以通过AllowOverride指令，来决定.htaccess 文件中可以生效的指令。

.htaccess 文件（或者”分布式配置文件”）提供了针对目录改变配置的方法，即在一个特定的文档目录中放置一个包含一个或多个指令的文件，以作用于此目录及其所有子目录。作为用户，所能使用的命令受到限制。管理员可以通过Apache的AllowOverride指令来设置。

子目录中的指令会覆盖更高级目录或者主服务器配置文件中的指令。

- .htaccess必须以ASCII模式上传，最好将其权限设置为644。

1、错误文档的定位

常用的客户端请求错误返回代码：
401 Authorization Required
403 Forbidden
404 Not Found
405 Method Not Allowed
408 Request Timed Out
411 Content Length Required
412 Precondition Failed
413 Request Entity Too Long
414 Request URI Too Long
415 Unsupported Media Type
常见的服务器错误返回代码：
500 Internal Server Error
用户可以利用.htaccess指定自己事先制作好的错误提醒页面。一般情况下，人们可以专门设立一个目录，例如errors放置这些页面。然后再.htaccess中，加入如下的指令：
ErrorDocument 404 /errors/notfound.html
ErrorDocument 500 /errors/internalerror.html
一条指令一行。上述第一条指令的意思是对于404，也就是没有找到所需要的文档的时候得显示页面为/errors目录下的notfound.html页面。不难看出语法格式为：
ErrorDocument 错误代码 /目录名/文件名.扩展名
如果所需要提示的信息很少的话，不必专门制作页面，直接在指令中使用HTML号了，例如下面这个例子：
ErrorDocument 401 “你没有权限访问该页面，请放弃！”

2、文档访问的密码保护

要利用.htaccess对某个目录下的文档设定访问用户和对应的密码，首先要做的是生成一个.htpasswd的文本文档，例如：
martin:123456
这里密码经过加密，用户可以自己找些工具将密码加密成.htaccess支持的编码。该文档最好不要放在www目录下，建议放在www根目录文档之外，这样更为安全些。
有了授权用户文档，可以在.htaccess中加入如下指令了：
AuthUserFile .htpasswd的服务器目录
AuthGroupFile /dev/null （需要授权访问的目录）
AuthName EnterPassword
AuthType Basic （授权类型）
require user wsabstract （允许访问的用户，如果希望表中所有用户都允许，可以使用 require valid-user）

3、拒绝来自某个IP的访问

如果我不想某个政府部门访问到我的站点的内容，那可以通过.htaccess中加入该部门的IP而将它们拒绝在外。

例如：
order allow,deny
deny from 210.10.56.32
deny from 219.5.45.
allow from all

第二行拒绝某个IP，第三行拒绝某个IP段，也就是219.5.45.0~219.2.45.255。想要拒绝所有人？用deny from all好了。不止用IP，也可以用域名来设定。

4、保护.htaccess文档

在使用.htaccess来设置目录的密码保护时，它包含了密码文件的路径。从安全考虑，有必要把.htaccess也保护起来，不让别人看到其中的内容。虽然可以用其他方式做到这点，比如文档的权限。不过，.htaccess本身也能做到，只需加入如下的指令：
order allow,deny
deny from all

5、URL转向

我们可能对网站进行重新规划，将文档进行了迁移，或者更改了目录。这时候，来自搜索引擎或者其他网站链接过来的访问就可能出错。这种情况下，可以通过如下指令来完成旧的URL自动转向到新的地址：
Redirect /旧目录/旧文档名 新文档的地址
或者整个目录的转向：
Redirect 旧目录 新目录

6、改变缺省的首页文件

一般情况下缺省的首页文件名有default、index等。不过，有些时候目录中没有缺省文件，而是某个特定的文件名，比如在pmwiki中是pmwiki.php。这种情况下，要用户记住文件名来访问很麻烦。在.htaccess中可以轻易的设置新的缺省文件名：
DirectoryIndex 新的缺省文件名
也可以列出多个，顺序表明它们之间的优先级别，例如：
DirectoryIndex filename.html index.cgi index.pl default.htm

7. 使用.htaccess 访止盗链。如果你网站上的一个图片被别的N多的网站引用了，那么，这很有可能会导致你服务器的性能下降，使用下面的代码可以保护某些热门的链接不被过多的引用。

Options +FollowSymlinks
# Protect Hotlinking
RewriteEngine On
RewriteCond %{HTTP_REFERER} !^$
RewriteCond %{HTTP_REFERER} !^http://(www.)?domainname.com/ [nc]
RewriteRule .*.(gif|jpg|png)$ http://domainname.com/img/hotlink_f_o.png [nc]

8. 设置网站的时区

SetEnv TZ America/Houston

9. 阻止IP列表

有些时候，你需要以IP地址的方式阻止一些访问。无论是对于一个IP地址还是一个网段，这都是一件非常简单的事情，如下所示：

allow from all
deny from 145.186.14.122
deny from 124.15

Apache对于被拒绝的IP会返回403错误。

10. 把一些老的链接转到新的链接上——搜索引擎优化SEO

Redirect 301 /d/file.html http://www.htaccesselite.com/r/file.html

11. 为服务器管理员设置电子邮件。

ServerSignature EMail
SetEnv SERVER_ADMIN default@domain.com

12. 阻止 User Agent 的所有请求

## .htaccess Code :: BEGIN
## Block Bad Bots by user-Agent
SetEnvIfNoCase user-Agent ^FrontPage [NC,OR]
SetEnvIfNoCase user-Agent ^<a href="http://www.blags.org/tags/java/">Java</a>.* [NC,OR]
SetEnvIfNoCase user-Agent ^Microsoft.URL [NC,OR]
SetEnvIfNoCase user-Agent ^MSFrontPage [NC,OR]
SetEnvIfNoCase user-Agent ^Offline.Explorer [NC,OR]
SetEnvIfNoCase user-Agent ^[Ww]eb[Bb]andit [NC,OR]
SetEnvIfNoCase user-Agent ^Zeus [NC]
Order Allow,Deny
Allow from all
Deny from env=bad_bot
## .htaccess Code :: END

13. 把某些特殊的IP地址的请求重定向到别的站点

ErrorDocument 403 http://www.youdomain.com
Order deny,allow
Deny from all
Allow from ip
Allow from ip

14. 直接找开文件而不是下载 – 通常，我们打开网上文件的时候总是会出现一个对话框问我们是下载还是直接打开，使用下面的设置就不会出现这个问题了，直接打开。

AddType application/octet-stream .pdf
AddType application/octet-stream .zip
AddType application/octet-stream .mov

15. 修改文件类型 – 下面的示例可以让任何的文件都成为PHP那么被服务器解释。比如：myphp, cgi，phtml等。

ForceType application/x-httpd-php
SetHandler application/x-httpd-php

16. 阻止存取.htaccess 文件

# secure htaccess file
order allow,deny
deny from all

17. 保护服务器上的文件被存取

# prevent access of a certain file order allow,deny
deny from all

18. 阻止目录浏览

# disable directory browsing
Options All -Indexes

19. 口令认证 – 你可以创建一个文件用于认证。下面是一个示例：

# to protect a file

AuthType Basic

AuthName “Prompt”

AuthUserFile /home/path/.htpasswd

Require valid-user

# password-protect a directory

resides

AuthType basic

AuthName “This directory is protected”

AuthUserFile /home/path/.htpasswd

AuthGroupFile /dev/null

Require valid-user
