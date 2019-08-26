---
date: 2012-12-18T23:17:33

title: .htaccess 规则

---

.htaccess 规则

1. Introduction 介绍
文件名 .htaccess 属性 644 (RW-R–R–)
htaccess会影响它所在目录下的所有子目录
注意大多数内容都要求保持在一行之内，不要换行，否则会引起错误

2. Error Documents 错误文档
Official document: ErrorDocument Directive
ErrorDocument code document
例子
ErrorDocument 400 /errors/badrequest.html
ErrorDocument 404 http://yoursite/errors/notfound.html
ErrorDocument 401 “Authorization Required”

more..
less.. (注意之后内容如果出现的双引号需要转义为 ”)
常见HTTP状态码
Successful Client Requests
200 OK
201 Created
202 Accepted
203 Non-Authorative Information
204 No Content
205 Reset Content
206 Partial Content
Client Request Redirected
300 Multiple Choices
301 Moved Permanently
302 Moved Temporarily
303 See Other
304 Not Modified
305 Use Proxy
Client Request Errors
400 Bad Request
401 Authorization Required
402 Payment Required (not used yet)
403 Forbidden
404 Not Found
405 Method Not Allowed
406 Not Acceptable (encoding)
407 Proxy Authentication Required
408 Request Timed Out
409 Conflicting Request
410 Gone
411 Content Length Required
412 Precondition Failed
413 Request Entity Too Long
414 Request URI Too Long
415 Unsupported Media Type
Server Errors
500 Internal Server Error
501 Not Implemented
502 Bad Gateway
503 Service Unavailable
504 Gateway Timeout
505 HTTP Version Not Supported

3. Password Protection 密码保护
Official document: Authentication, Authorization and Access Control
假设密码文件为.htpasswd
AuthUserFile /usr/local/safedir/.htpasswd (这里必须使用全路径名)
AuthName EnterPassword
AuthType Basic
两种常见验证方式:
Require user windix
(仅允许用户windix登陆)
Require valid-user
(所有合法用户都可登陆)
Tip: 如何生成密码文件
使用htpasswd命令(apache自带)
第一次生成需要创建密码文件
htpasswd -c .htpasswd user1
之后增加新用户
htpasswd .htpasswd user2

4. Enabling SSI Via htaccess 通过htaccess允许SSI(Server Side Including)功能
AddType text/html .shtml
AddHandler server-parsed .shtml
Options Indexes FollowSymLinks Includes
DirectoryIndex index.shtml index.html

5. Blocking users by IP 根据IP阻止用户访问
order allow,deny
deny from 123.45.6.7
deny from 12.34.5. (整个C类地址)
allow from all

6. Blocking users/sites by referrer 根据referrer阻止用户/站点访问
需要mod_rewrite模块
例1. 阻止单一referrer: badsite.com
RewriteEngine on
# Options +FollowSymlinks
RewriteCond %{HTTP_REFERER} badsite.com [NC]
RewriteRule .* - [F]
例2. 阻止多个referrer: badsite1.com, badsite2.com
RewriteEngine on
# Options +FollowSymlinks
RewriteCond %{HTTP_REFERER} badsite1.com [NC,OR]
RewriteCond %{HTTP_REFERER} badsite2.com
RewriteRule .* - [F]
[NC] - 大小写不敏感(Case-insensite)
[F] - 403 Forbidden
注意以上代码注释掉了”Options +FollowSymlinks”这个语句。如果服务器未在 httpd.conf 的 段落设置 FollowSymLinks, 则需要加上这句，否则会得到”500 Internal Server error”错误。

7. Blocking bad bots and site rippers (aka offline browsers) 阻止坏爬虫和离线浏览器
需要mod_rewrite模块
坏爬虫? 比如一些抓垃圾email地址的爬虫和不遵守robots.txt的爬虫(如baidu?)
可以根据 HTTP_USER_AGENT 来判断它们
(但是还有更无耻的如”中搜 zhongsou.com”之流把自己的agent设置为 “Mozilla/4.0 (compatible; MSIE 5.5; Windows NT 5.0)” 太流氓了，就无能为力了)
RewriteEngine On
RewriteCond %{HTTP_USER_AGENT} ^BlackWidow [OR]
RewriteCond %{HTTP_USER_AGENT} ^Bot mailto:craftbot@yahoo.com [OR]
RewriteCond %{HTTP_USER_AGENT} ^ChinaClaw [OR]
RewriteCond %{HTTP_USER_AGENT} ^Custo [OR]
RewriteCond %{HTTP_USER_AGENT} ^DISCo [OR]
RewriteCond %{HTTP_USER_AGENT} ^Download Demon [OR]
RewriteCond %{HTTP_USER_AGENT} ^eCatch [OR]
RewriteCond %{HTTP_USER_AGENT} ^EirGrabber [OR]
RewriteCond %{HTTP_USER_AGENT} ^EmailSiphon [OR]
RewriteCond %{HTTP_USER_AGENT} ^EmailWolf [OR]
RewriteCond %{HTTP_USER_AGENT} ^Express WebPictures [OR]
RewriteCond %{HTTP_USER_AGENT} ^ExtractorPro [OR]
RewriteCond %{HTTP_USER_AGENT} ^EyeNetIE [OR]
RewriteCond %{HTTP_USER_AGENT} ^FlashGet [OR]
RewriteCond %{HTTP_USER_AGENT} ^GetRight [OR]
RewriteCond %{HTTP_USER_AGENT} ^GetWeb! [OR]
RewriteCond %{HTTP_USER_AGENT} ^Go!Zilla [OR]
RewriteCond %{HTTP_USER_AGENT} ^Go-Ahead-Got-It [OR]
RewriteCond %{HTTP_USER_AGENT} ^GrabNet [OR]
RewriteCond %{HTTP_USER_AGENT} ^Grafula [OR]
RewriteCond %{HTTP_USER_AGENT} ^HMView [OR]
RewriteCond %{HTTP_USER_AGENT} HTTrack [NC,OR]
RewriteCond %{HTTP_USER_AGENT} ^Image Stripper [OR]
RewriteCond %{HTTP_USER_AGENT} ^Image Sucker [OR]
RewriteCond %{HTTP_USER_AGENT} Indy Library [NC,OR]
RewriteCond %{HTTP_USER_AGENT} ^InterGET [OR]
RewriteCond %{HTTP_USER_AGENT} ^Internet Ninja [OR]
RewriteCond %{HTTP_USER_AGENT} ^JetCar [OR]
RewriteCond %{HTTP_USER_AGENT} ^JOC Web Spider [OR]
RewriteCond %{HTTP_USER_AGENT} ^larbin [OR]
RewriteCond %{HTTP_USER_AGENT} ^LeechFTP [OR]
RewriteCond %{HTTP_USER_AGENT} ^Mass Downloader [OR]
RewriteCond %{HTTP_USER_AGENT} ^MIDown tool [OR]
RewriteCond %{HTTP_USER_AGENT} ^Mister PiX [OR]
RewriteCond %{HTTP_USER_AGENT} ^Navroad [OR]
RewriteCond %{HTTP_USER_AGENT} ^NearSite [OR]
RewriteCond %{HTTP_USER_AGENT} ^NetAnts [OR]
RewriteCond %{HTTP_USER_AGENT} ^NetSpider [OR]
RewriteCond %{HTTP_USER_AGENT} ^Net Vampire [OR]
RewriteCond %{HTTP_USER_AGENT} ^NetZIP [OR]
RewriteCond %{HTTP_USER_AGENT} ^Octopus [OR]
RewriteCond %{HTTP_USER_AGENT} ^Offline Explorer [OR]
RewriteCond %{HTTP_USER_AGENT} ^Offline Navigator [OR]
RewriteCond %{HTTP_USER_AGENT} ^PageGrabber [OR]
RewriteCond %{HTTP_USER_AGENT} ^Papa Foto [OR]
RewriteCond %{HTTP_USER_AGENT} ^pavuk [OR]
RewriteCond %{HTTP_USER_AGENT} ^pcBrowser [OR]
RewriteCond %{HTTP_USER_AGENT} ^RealDownload [OR]
RewriteCond %{HTTP_USER_AGENT} ^ReGet [OR]
RewriteCond %{HTTP_USER_AGENT} ^SiteSnagger [OR]
RewriteCond %{HTTP_USER_AGENT} ^SmartDownload [OR]
RewriteCond %{HTTP_USER_AGENT} ^SuperBot [OR]
RewriteCond %{HTTP_USER_AGENT} ^SuperHTTP [OR]
RewriteCond %{HTTP_USER_AGENT} ^Surfbot [OR]
RewriteCond %{HTTP_USER_AGENT} ^tAkeOut [OR]
RewriteCond %{HTTP_USER_AGENT} ^Teleport Pro [OR]
RewriteCond %{HTTP_USER_AGENT} ^VoidEYE [OR]
RewriteCond %{HTTP_USER_AGENT} ^Web Image Collector [OR]
RewriteCond %{HTTP_USER_AGENT} ^Web Sucker [OR]
RewriteCond %{HTTP_USER_AGENT} ^WebAuto [OR]
RewriteCond %{HTTP_USER_AGENT} ^WebCopier [OR]
RewriteCond %{HTTP_USER_AGENT} ^WebFetch [OR]
RewriteCond %{HTTP_USER_AGENT} ^WebGo IS [OR]
RewriteCond %{HTTP_USER_AGENT} ^WebLeacher [OR]
RewriteCond %{HTTP_USER_AGENT} ^WebReaper [OR]
RewriteCond %{HTTP_USER_AGENT} ^WebSauger [OR]
RewriteCond %{HTTP_USER_AGENT} ^Website eXtractor [OR]
RewriteCond %{HTTP_USER_AGENT} ^Website Quester [OR]
RewriteCond %{HTTP_USER_AGENT} ^WebStripper [OR]
RewriteCond %{HTTP_USER_AGENT} ^WebWhacker [OR]
RewriteCond %{HTTP_USER_AGENT} ^WebZIP [OR]
RewriteCond %{HTTP_USER_AGENT} ^Wget [OR]
RewriteCond %{HTTP_USER_AGENT} ^Widow [OR]
RewriteCond %{HTTP_USER_AGENT} ^WWWOFFLE [OR]
RewriteCond %{HTTP_USER_AGENT} ^Xaldon WebSpider [OR]
RewriteCond %{HTTP_USER_AGENT} ^Zeus
RewriteRule ^.* - [F,L]
[F] - 403 Forbidden
[L] - ?

8. Change your default directory page 改变缺省目录页面
DirectoryIndex index.html index.php index.cgi index.pl

9. Redirects 转向
单个文件
Redirect /old_dir/old_file.html http://yoursite.com/new_dir/new_file.html
整个目录
Redirect /old_dir http://yoursite.com/new_dir
效果: 如同将目录移动位置一样
[url]http://yoursite.com/old_dir -&gt; http://yoursite.com/new_dir[/url]
[url]http://yoursite.com/old_dir/dir1/test.html -&gt; http://yoursite.com/new_dir/dir1/test.html[/url]
Tip: 使用用户目录时Redirect不能转向的解决方法
当你使用Apache默认的用户目录，如 http://mysite.com/~windix，当你想转向 http://mysite.com/~windix/jump时，你会发现下面这个Redirect不工作:
Redirect /jump http://www.google.com
正确的方法是改成
Redirect /~windix/jump http://www.google.com
(source: .htaccess Redirect in “Sites” not redirecting: why?
)

10. Prevent viewing of .htaccess file 防止.htaccess文件被查看
order allow,deny
deny from all

11. Adding MIME Types 添加 MIME 类型
AddType application/x-shockwave-flash swf
Tips: 设置类型为 application/octet-stream 将提示下载

12. Preventing hot linking of images and other file types 防盗链
需要mod_rewrite模块
RewriteEngine on
RewriteCond %{HTTP_REFERER} !^$
RewriteCond %{HTTP_REFERER} !^http://(www/.)?mydomain.com/.*$ [NC]
RewriteRule .(gif|jpg|js|css)$ - [F]
解析:
若 HTTP_REFERER 非空 (来源为其他站点，非直接连接) 并且
若 HTTP_REFERER 非(www.)mydomain.com开头(忽略大小写[NC]) (来源非本站)
对于所有含有 .gif/.jpg/.js/.css 结尾的文件给出 403 Forbidden 错误[F]
也可指定响应，如下例显示替换图片
RewriteRule .(gif|jpg)$
[R,L]
[R] - 转向(Redirect)
[L] - 连接(Link)
