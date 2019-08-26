---
date: 2012-12-18T23:18:51

title: url_rewrite_rule

---

开启mod_rewrite
开启mod_rewrite模块
使用URL重写功能，需要安装mod_rewrite模块。使用phpinfo()函数，找到Apache Modules section部分，可以看到当前apache加载模块。
如果没有开启mod_rewrite, 就需要配置mod_rewrite.so的路径：
LoadModule rewrite_module modules/mod_rewrite.so
Apache2内置了mod_rewirte，在配置虚拟主机的配置文件VirtualHost部分打开引擎：RewriteEngine on
这句之后就可以使用重写语法了。
VirtualHost文件部分的Directory配置必须是：
···
Options Includes FollowSymLinks #允许使用符号链接
AllowOverride All #允许目录配置文件.htaccess。如果不使用是AllowOverride None
···
利用mod_rewrite重写URL主要使用两个基本的指令RewriteRule和RewriteCond。

RewriteRule指令
RewriteRule Pattern Substitution [Flags]
在模式（Pattern）和替换（Substitution）中使用正则表达式来匹配相应的字符。
譬如有如下的URL：
http://www.example.com/display.php?country=USA &amp;state=California&amp;city=San_Diego
REQUEST_URI的值是“/country=USA &amp;state=California&amp;city=San_Diego”，要将国家州城市信息更友好的显示给用，要显示成这样：

http://www.example.com/USA/California/San_Diego

一个最常用的正则就是（.*）。它含有两个元素：一是“点”，表示任 意字符；二是“星”，表示以前的全部字符。所以（.*）会匹配{REQUEST_URI}的所有字符。Rewrite重写引擎的输入串是 {REQUEST_URI}，也就是URL中出去域名以及“?”符号后的所有查询字符。
重定向的URL中要提取出“USA/California/San_Diego”，匹配模式正则表达式的原型是：
(.*)/(.*)/(.*)
以上正则，在{REQUEST_URI}中通过两个“/”的分割存储了三个值，为了解决我们具体问题，我们得加一点限制――毕竟，第一个和最后一个原子可以匹配任何字符。
开始，我们可以添加一些特殊的字符，比如表示正则“开始”或者“结束”，“^”字符表示正则的开始而“$”表示正则的结束。
^(.*)/(.*)/(.*)$
{REQUEST_URI}是以“/”开头。Apache 在更改版本的时候会更改正则引擎，一代Apache要求有斜杠而二代Apache却不允许！但是我们可以用^/?（？表示匹配字符本身或者前一个字符）来 兼容两个版本的Apache。再加上对字符匹配的限制，最终匹配模式是：
^/?([a-zA-Z_]+)/([a-zA-Z_]+)/([a-zA-Z_]+)$
完整表示如下：
RewriteEngine on
RewriteRule ^/?([a-zA-Z_]+)/([a-zA-Z_]+)/([a-zA-Z_]+)$ display.php?country=$1 &amp;state=$2&amp;city=$3 [L]
RewriteRule使用$1到$9引用模式()中匹配的内容，称为反向引用。对于URL：

http://www.example.com/USA/California/San_Diego

$1=USA，$2=California，$3=San_Diego

RewriteRule选项
“redirect|R[=code]” 强制重定向。经常引用到触发可见的定向。默认情况下它是一个HTTP 302的临时重定向，但是你可以注明具体的HTTP 代码，比如你可以用[R=301]来表明这是一个永久重定向，这对搜索引擎抓取你重定向后的网页相当有用。
“proxy|P” 强制为代理
“forbidden|F” 403 禁止。告诉Apache响应请求时不提供页面。其原理就是Apache会发出一个403 HTTP相应，可以保护网站不被未经授权的或者其他盗链访问。
“nocase|NC” 忽略正则表达式中的大小写。它经常被用到{HTTP_HOST}服务器参数上，因为域名里面是不会区分大小写的。
“next|N” 回到第一条规则。可以让你的重写条件循环匹配，当你不知道{REQUEST_URI}有多少字符进行匹配的时候很有用。
“last|L” 最后一个规则。告诉Apache服务器一系列的条件或者是规则将在它出现后结束，换句话说就是[L]不出现，mod_rewrite将会一直执行。
“noescape|NE” 在输出中不对URI作转义。此标记阻止mod_rewrite对重写结果应用常规的URI转义规则。 一般情况下，特殊字符(如‘%’, ‘$’, ‘;’等)会被转义为等值的十六进制编码。 此标记可以阻止这样的转义，以允许百分号等符号出现在输出中，如：
RewriteRule /foo/(.*) /bar?arg=P1\%3d$1 [R,NE]
可以使‘/foo/zed’转向到一个安全的请求‘/bar?arg=P1=zed’.
“skip|S=N” 跳过下面的N条规则。

RewriteCond指令
RewriteCond TestString CondPattern [Flags]
RewriteCond指令定义了一个规则的条件，即在一个RewriteRule指令之前有一个或多个RewriteCond指令。 条件之后的重写规则仅在当前URI与pattern匹配并且符合这些条件的时候才会起作用。
RewriteCond也有反向引用，但和RewriteRule中用$N引用不同，它使用%N反向引用。
RewriteCond引用Apache变量%{ NAME_OF_VARIABLE}，如%{HTTP_HOST}。
RewriteCond的条件模式（CondPattern）除了使用perl样式正则表达式，还有额外的规则：
1. 使用‘!’ 字符(惊叹号)来实现匹配的反转
2. ‘,=。
3. ‘-d’ (是一个目录[directory])。将TestString视为一个路径名并测试它是否存在而且是一个目录.
4. ‘-f’ (是一个常规的文件[file])。将TestString视为一个路径名并测试它是否存在而且是一个常规的文件.
5. ‘-s’ (是一个非空的常规文件[size])。将TestString视为一个路径名并测试它是否存在而且是一个尺寸大于0的常规的文件.
6. ‘-l’ (是一个符号连接[link])。将TestString视为一个路径名并测试它是否存在而且是一个符号连接.
7. ‘-F’ (对子请求有效的业已存在的文件)。测试TestString是否一个有效的文件， 而且可以被服务器当前已经配置的所有存取控制所存取。 它用一个内部子请求来做判断，由于会降低服务器的性能，请小心使用!

RewriteCond选项
‘nocase|NC’ (no case)。它使测试忽略大小写。此标记仅作用于TestString和CondPattern的比较， 而对文件系统和子请求的测试不起作用。
‘ornext|OR’ (or next condition)。它以OR方式组合若干规则的条件，而不是默认的AND。典型的例子如下：
RewriteCond %{REMOTE_HOST} ^host1.* [OR]
RewriteCond %{REMOTE_HOST} ^host2.* [OR]
RewriteCond %{REMOTE_HOST} ^host3.*
RewriteRule …some special stuff for any of these hosts…
如果不用这个标记，则必须使用三个 条件/规则。

服务器变量
HTTP变量
HTTP_USER_AGENT, HTTP_REFERER, HTTP_COOKIE,
HTTP_FORWARDED, HTTP_HOST, HTTP_PROXY_CONNECTION, HTTP_ACCEPT
连结和请求的变量
REMOTE_ADDR, REMOTE_HOST, REMOTE_USER, REMOTE_IDENT,
REQUEST_METHOD, SCRIPT_FILENAME, PATH_INFO, QUERY_STRING, AUTH_TYPE
服务器内部变量
DOCUMENT_ROOT, SERVER_ADMIN, SERVER_NAME, SERVER_ADDR,
SERVER_PORT, SERVER_PROTOCOL, SERVER_SOFTWARE
系统变量
TIME_YEAR, TIME_MON, TIME_DAY, TIME_HOUR,
TIME_MIN, TIME_SEC, TIME_WDAY, TIME
mod_rewrite特殊值
API_VERSION, THE_REQUEST, REQUEST_URI, REQUEST_FILENAME

URL重写举例
1. 给子域名加www标记
RewriteCond %{HTTP_HOST} ^([a-z.]+)?example\.com$ [NC]
RewriteCond %{HTTP_HOST} !^www\. [NC]
RewriteRule .? http://www.%1example.com%{REQUEST_URI} [R=301,L]
这个规则抓取二级域名的%1变量，如果不是以www开始，那么就加www，以前的域名以及{REQUEST_URI}会跟在其后。

1. 去掉域名中的www标记
RewriteCond %{HTTP_HOST} !^example\.com$ [NC]
RewriteRule .? http://example.com%{REQUEST_URI} [R=301,L]

1. 去掉www标记，但是保存子域名
RewriteCond %{HTTP_HOST} ^www\.(([a-z0-9_]+\.)?example\.com)$ [NC]
RewriteRule .? http://%1%{REQUEST_URI} [R=301,L]
这里，当匹配到1%变量以后，子域名才会在%2（内部原子）中抓取到，而我们需要的正是这个%1变量。

1. 防止图片盗链
一些站长不择手段的将你的图片盗链在他们网站上，耗费你的带宽。你可以加一下代码阻止这种行为。
RewriteCond %{HTTP_REFERER} !^$
RewriteCond %{HTTP_REFERER} !^http://(www\.)?example\.com/ [NC]
RewriteRule \.(gif|jpg|png)$ – [F]
如果{HTTP_REFERER}值不为空，或者不是来自你自己的域名，这个规则用[F]FLAG阻止以gif|jpg|png 结尾的URL
如果对这种盗链你是坚决鄙视的，你还可以改变图片，让访问盗链网站的用户知道该网站正在盗用你的图片。
RewriteCond %{HTTP_REFERER} !^$
RewriteCond %{HTTP_REFERER} !^http://(www\.)?example\.com/.*$ [NC]
RewriteRule \.(gif|jpg|png)$ http://www.example.com/hotlinked.gif [R=301,L]
除了阻止图片盗链链接，以上规则将其盗链的图片全部替换成了你设置的图片。
你还可以阻止特定域名盗链你的图片：
RewriteCond %{HTTP_REFERER} !^http://(www\.)?leech_site\.com/ [NC]
RewriteRule \.(gif|jpg|png)$ – [F,L]
这个规则将阻止域名黑名单上所有的图片链接请求。
当然以上这些规则都是以{HTTP_REFERER}获取域名为基础的，如果你想改用成IP地址，用{REMOTE_ADDR}就可以了。

5. 如果文件不存在重定向到404页面
RewriteCond %{DOCUMENT_ROOT}%{REQUEST_FILENAME} !-f
RewriteCond %{DOCUMENT_ROOT}%{REQUEST_FILENAME} !-d
RewriteRule .? /404.php [L]
-f匹配的是存在的文件名，-d匹配的存在的路径名

6. 创建无文件后缀名链接
RewriteCond %{REQUEST_FILENAME}.php -f
RewriteRule ^/?([a-zA-Z0-9]+)$ $1.php [L]
RewriteCond %{REQUEST_FILENAME}.html -f
RewriteRule ^/?([a-zA-Z0-9]+)$ $1.html [L]
如果文件是以.php为后缀，这条规则将被执行。

7. 强制使用HTTPS
RewriteCond %{HTTPS} !on
#RewriteCond %{SERVER_PORT} !^443$
RewriteCond %{HTTP_HOST} ^([a-z.]+)?example\.com$ [NC]
RewriteRule ^(.*)$ https://%1example.com$1 [R=301,L]
判断HTTPS服务可以判断安全端口（一般是443），也可以通过HTTPS变量。将example.com域名下所有url都强制使用https服务。
如果不判断域名，可以这样：
RewriteCond %{HTTPS} !on
RewriteRule ^/?(.*)$ https://%{SERVER_NAME}/$1 [R=301,L]
这里的$1前面有斜杠/，其实是匹配模式去掉了斜杠的原因，和上面效果是一样的。

推荐资源
>正则表达式工具firefox扩展：Regular Expressions Tester
正则表Introduction to the Tutorial：
http://gnosis.cx/publish/programming/regular_expressions.html
Apache文档之mod_rewrite介绍：
http://www.uplinux.com/download/doc/apache/ApacheManual/mod/mod_rewrite.html
Apache文档之URL重写指南：
http://www.uplinux.com/download/doc/apache/ApacheManual/misc/rewriteguide.html
Learn Apache mod_rewrite: 13 Real-world Examples，原文：
http://www.sitepoint.com/article/apache-mod_rewrite-examples/
Learn Apache mod_rewrite: 13 Real-world Examples，译文：http://www.tsingfeng.com/?p=357
A Beginner’s Guide to URL Rewriting：
http://www.sitepoint.com/article/guide-url-rewriting/
Apache的Mod_rewrite 例子：
http://dreamwaver.bokee.com/5692845.html
