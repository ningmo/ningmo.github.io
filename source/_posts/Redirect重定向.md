---
date: 2012-12-18T23:15:44

title: Redirect重定向

---

Redirect指令是.htaccess文件中一个非常常用的指令，它的作用是发送一个外部重定向使客户端重新跳转到一个不同的URL。

Redirect重定向指令有三个不同的状态参数：

第一个是permanent：永久性301重定向；
第二个是temp：临时重定向状态码，即返回302；
第三个是seeother：此参数表示“参见”的含义，即本资源已经被另外一个更好的资源替代了；

另外，还有一个gone，表示此资源已经被永久性删除了，因此，也就不存在什么跳转关系了。其后面的URL会被忽略。但一般来说，对于SEO上的用途，301永久跳转是用的最多的。

比如，如果希望网页从本空间的 /service目录跳转到 http://foo2.example.com/service ，那么，则可以使用如下htaccess文件指令：

Redirect [permanent,temp,seeother] /service http://foo2.example.com/service

此时，如果客户端请求http://example.com/service/foo.txt，则会被重定向到http://foo2.example.com/service/foo.txt。通过例子可以看出，此时的<strong>重定向是带有广泛匹配</strong>的，即：该目录下的所有子文档都会被<strong>重定向到一一对应的</strong>新地址上去。这一特性也给htaccess文件的编写带来了非常大的方便。

因为Redirect指令进匹配完整的URL，因此，该命令不能用来匹配文件名的正则表达式。如果希望匹配更多的条件，请使用RedirectMatch命令。
后文将进行详细的介绍：

<strong>RedirectMatch 指令</strong>：

RedirectMatch指令是基于正则表达式匹配对当前的URL发送一个外部重定向命令。该命令与Redirect等效，但是URL能使用正则匹配。它使用了标准的正则表达式而不是简单的文件匹配。

<strong>其语法是：</strong>

RedirectMatch [status] regex URL

如果egex与URL相匹配，则服务器会把所匹配的括弧中的字符串替换到该指令所指定的目标字符串中，并将它视之为一个文件名。例如，重定向所有GIF文件到另一个服务器上同名的JPEG文件，可以：

RedirectMatch (.*)\.gif$ http://www.anotherserver.com$1.jpg

因此，该命令更加强大。其实际作用在于，比如想将本服务器上的所有图片、rar等资源换到专用的FTP服务器上去，这个功能就起大作用了。

与上述的301重定向命令<strong>类似的还有RedirectPermanent指令</strong>，与之对应的302重定向还有RedirectTemp指令。他们在用法上都是大同小异，只是这两个指令将之前的那两个指令具体化了一下，并且不带有状态码参数。

比如：

RedirectPermanent /service http://foo2.exsample.com/service 等效于 Redirect Permanent /service http://foo2.exsample.com/service

<strong>无论使用哪种表达都具有同样的效果。</strong>

301永久重定向将会促使搜索引擎尽快更新收录的地址并且收录新的地址。但是，某些情况下，这一方法可能会让搜索引擎作为<strong>作弊处理</strong>。这些指令的作用域都包含.htaccess配置文件，直接将需要的指令加入该文件

<strong>.htaccess文件在线生成器地址</strong>：<a href="http://blog.wangqu.org/htaccess/" target="_blank">http://blog.wangqu.org/htaccess/</a>
