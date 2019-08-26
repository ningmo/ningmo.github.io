---
date: 2012-12-18T23:13:34

title: modrewite 将URL定向到另一个目录或文件，而地址栏URL不变，网页内容为新地址内容

---

<div>modrewite 将URL定向到另一个目录或文件，而地址栏URL不变，网页内容为新地址内容。 http://www.test.com/a/b 显示 http://www.test.com/test/的内容，URL地址还保持为 www.test.com/a/b根据下面操作自己可以更加灵活...</div>
<div>
<div>modrewite 将URL定向到另一个目录或文件，而地址栏URL不变，网页内容为新地址内容。</div>
<div>http://www.test.com/a/b   显示 http://www.test.com/test/的内容，URL地址还保持为</div>
<div>www.test.com/a/b
根据下面操作自己可以更加灵活变动</div>
<div></div>
<div>参考连接：<a href="http://www.itlearner.com/article/2007/3748.shtml" target="_blank">Apache 重写规则的常见应用</a>

一、实现原理：
（主要看第2点）
只有当用户的WEB请求最终被导向到某台WEB服务器的Apache后台，则这台WEB服务器接受
进来的请求，根据配置文件该请求是主配置还是虚拟主机，再根据用户在浏览器中请求的
URI来配对重写规则并且根据实际的请求路径配对.htaccess中的重写规则。最后把请求
的内容传回给用户，该响应可能有两种：</div>
<div>      1) 对浏览器请求内容的外部重定向(Redirect)到另一个URL。
让浏览器再次以新的URI发出请求(R=301或者R=302，临时的或是永久的重定向)
如：一个网站有正规的URL和别名URL，对别名URL进行重定向到正规URL，或者网站改换
成了新的域名
则把旧的域名重定向到新的域名(Redirect)</div>
<div>      2) 也可能是由Apache内部子请求代理产生新的内容送回给客户[P,L]
这是Apache内部根据重写后的URI内部通过代理模块请求内容并送回内容给客户，而客户
端浏览器并
不知道，浏览器中的URI不会被重写。但实际内容被Apache根据重写规则后的URI得到。
如：在公司防火墙上运行的Apache启动这种代理重写规则，代理对内部网段上的WEB服务
器的请求。

说明：为了不让地址URL被重写，而内容重定向，使用apache mod_proxy代理模块的反向代理功能</div>
<div>实现。</div>
<div>二、简单说一下mod_proxy
Apache可以被配置为正向(forward)和反向(reverse)代理。</div>
<div>正向代理是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得</div>
<div>内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并</div>
<div>将获得的内容返回给客户端。客户端必须要进行一些特别的设置才能使用正向代理。</div>
<div>正向代理的典型用途是为在防火墙内的局域网客户端提供访问Internet的途径。正向代理还可以</div>
<div>使用缓冲特性(由mod_cache提供)减少网络使用率。</div>
<div>使用ProxyRequests指令即可激活正向代理。因为正向代理允许客户端通过它访问任意网站并且隐</div>
<div>藏客户端自身，因此你必须采取安全措施以确保仅为经过授权的客户端提供服务。</div>
<div>反向代理正好相反，对于客户端而言它就像是原始服务器，并且客户端不需要进行任何特别的设</div>
<div>置。客户端向反向代理的名字空间(name-space)中的内容发送普通请求，接着反向代理将判断向</div>
<div>何处(原始服务器)转交请求，并将获得的内容返回给客户端，就像这些内容原本就是它自己的一</div>
<div>样。</div>
<div>反向代理的典型用途是将防火墙后面的服务器提供给Internet用户访问。反向代理还可以为后端</div>
<div>的多台服务器提供负载平衡，或为后端较慢的服务器提供缓冲服务。另外，还可以启用高级URL策</div>
<div>略和管理技术，从而使处于不同web服务器系统的web页面同时存在于同一个URL空间下。</div>
<div>可以使用ProxyPass指令激活反向代理(在RewriteRule指令中使用[P]标记也可以)。配置反向代理</div>
<div>并不需要打开ProxyRequests指令。</div>
<div>简单示例：</div>
<div>正向代理
ProxyRequests On
ProxyVia On</div>
<div>&lt;Proxy *&gt;</div>
<div>Order deny,allow
Deny from all
Allow from internal.example.com</div>
<div>&lt;/Proxy&gt;</div>
<div>反向代理
ProxyRequests Off</div>
<div>&lt;Proxy *&gt;</div>
<div>Order deny,allow
Allow from all</div>
<div>&lt;/Proxy&gt;</div>
<div>ProxyPass /foo http://foo.example.com/bar
ProxyPassReverse /foo http://foo.example.com/bar</div>
<div>
三、mod_rewrite的具体使用方法，我在这里就不多说了，请看这个链接：
http://bbs.chinaunix.net/viewthread.php?tid=66521&amp;extra=page%3D2</div>
<div>四、开始安装!!!!!!</div>
<div>仔细看上面理论说明，具体操作没有几步的，越来越觉得理论基础非常重要！！！</div>
<div>1、安装apache2.0
cd apache2.0
./configure --enable-MODULE=shared --enable-so --with-mpm=worker  --enable-</div>
<div>rewrite=shared  --enable-proxy=shared</div>
<div>说明：
--enable-proxy=shared  加入mod_proxy模块</div>
<div>--enable-rewrite=shared  加入mod_rewrite模块</div>
<div>对于编译mod_rewrite模块，我多说几句：</div>
<div>按照上面的方法，rewrite模块已经正常编译进了./apache2/module/目录里了，而且配置文件</div>
<div>LoadModule也添加相关路径了，可是我使用时rewrite却不能重写，所以我又手动添加了一次，这</div>
<div>个方法同样也适合不想重新安装apache，采用手动添加的方法。</div>
<div>
cd 源代码目录
find ./ -name mod_rewrite.c
cd PATH/to/mod_rewrite.c
/usr/local/apache2/bin/apxs -c mod_rewrite.c
/usr/local/apache2/bin/apxs -i -a -n mod_rewrite mod_rewrite.la

如果出现这个错误
Syntax error on line 329 of /usr/local/apache2/conf/httpd.conf:
Can`t loacte API module staructure `mod_rewrite_module` in file</div>
<div>/usr/local/apache2/modules/mod_rewrite.so:/usr/local/apache2/lib/libapr-</div>
<div>0.so.0:undefined symbol:mod_rewrite_module
修改http.conf
原来：httpd.conf里面写的mod_rewrite_module
改成 rewrite_module

如果重启apache出现这个错误module rewrite_module is built-in and can`t be loaded
表示模块是内建的，不用再调入
结束。</div>
<div>2、修改配置文件
vi conf/httpd.conf
添加下面内容：
&lt;Proxy *&gt;
Order deny,allow
Allow from all
&lt;/Proxy&gt;
启用mod_proxy反向代理</div>
<div>NameVirtualHost 192.168.0.203:80
&lt;VirtualHost 192.168.0.203:80&gt;
ServerAdmin test@test.com
DocumentRoot /test
ServerName www.test.com
ErrorLog logs/ip-error_log
RewriteEngine on
RewriteCond %{REQUEST_URI} /[^&amp;]+/b/
RewriteCond %{REQUEST_URI} !/test/
RewriteCond %{HTTP_HOST} ^www.test.com [NC]
RewriteRule ^/(.*) http://www.test.com/test/ [P,L]
&lt;/VirtualHost&gt;</div>
<div>这几个条件和规则的意思是：如果输入www.test.com  （[^&amp;]+表示任意字符）并且后面的URI为</div>
<div>a/b/形式，则跳到www.test.com/test/ 如果输入的是www.test.com/test/则不跳转。[P]这个参</div>
<div>数很重要，使用反向代理方式应该用户请求。</div>
<div>经过上面这几步，就是可以实现前面的需求。</div>
</div>
