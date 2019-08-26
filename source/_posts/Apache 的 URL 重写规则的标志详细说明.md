---
date: 2012-12-18T23:23:03

title: Apache 的 URL 重写规则的标志详细说明

---

1) R[=code](force redirect) 强制外部重定向 强制在替代字符串加上 http://thishost/[:thisport]/前缀重定向到外部的 URL. 如果 code 不指定，将用缺省的 302 HTTP 状态码。
2) F(force URL to be forbidden)禁用 URL,返回 403HTTP 状态码。
3) G(force URL to be gone) 强制 URL 为 GONE，返回 410HTTP 状态码。
4) P(force proxy) 强制使用代理转发。
5) L(last rule) 表明当前规则是最后一条规则，停止分析以后规则的重写。
6) N(next round) 重新从第一条规则开始运行重写过程。
7) C(chained with next rule) 与下一条规则关联 如果规则匹配则正常处理，该标志无效，如果不匹配，那么下面所有关联的 规则都跳过。
8) T=MIME-type(force MIME type) 强制 MIME 类型
9) NS (used only if no internal sub-request) 只用于不是内部子请求
10）NC(no case) 不区分大小写
11) QSA(query string append) 追加请求字符串
12) NE(no URI escaping of output) 不在输出转义特殊字符 例 如 ： RewriteRule /foo/(.*) /bar?arg=P1\%3d$1 [R,NE] 将 能 正 确 的 将 /foo/zoo 转换成/bar?arg=P1=zed
13) PT(pass through to next handler) 传递给下一个处理 例如： RewriteRule ^/abc(.*) /def$1 [PT] # 将会交给/def 规则处理 Alias /def /ghi
14) S=num(skip next rule(s)) 跳过 num 条规则
15) E=VAR:VAL(set environment variable) 设置环境变量 Rewrite 规则表达式的说明： . 匹配任何单字符 wFlv i =n/ [chars] 匹配字符串:chars[o c~iDx%W [^chars] 不匹配字符串:charsLc&lt;eRVNd, text1|text2 可选择的字符串:text1 或 text2{ OT:3SS7 ? 匹配 0 到 1 个字符 A%Z)w z{ * 匹配 0 到多个字符 v4 *rPG v + 匹配 1 到多个字符" o c $ ^ 字符串开始标志 SKXBrD=$ 字符串结束标志&amp;LQ% \n 转义符标志 反向引用 $N 用于 RewriteRule 中匹配的变量调用(0 &lt;= N &lt;= 9)=_=*O EgO] 反向引用 %N 用于 RewriteCond 中最后一个匹配的变量调用(1 &lt;= N &lt;= 9) 实际操作 例子： RewriteEngine on RewriteCond %{HTTP_USER_AGENT} ^MSIE [NC,OR]_R N/7\ RewriteCond %{HTTP_USER_AGENT} ^Opera [NC]!&amp;' # a RewriteRule ^.* - [F,L] 这里”-”表示没有替换，浏览器为 IE 和 Opera 的访客将 被禁止访问。 例子：*U( 1iv0 n RewriteEngine OnQWU5-p9e 8 RewriteBase /testQ3 K ;kS RewriteCond %{REQUEST_FILENAME}.php -frU/-Wq` B RewriteRule ([^/]+)$ /test/$1.php?7r mw y\ #for example: /test/admin =&gt; /test/admin.phpI v 80,hW RewriteRule ([^/]+)\.html$ /test/$1.php [L]aufcd57 #for example: /test/admin.html =&gt; /test/admin.php 限制目录只能显示图片 yKX: Z4I/ &lt; IfModule mod_rewrite.c&gt;+ #i ,87 RewriteEngine onL @ ^ !( RewriteCond %{REQUEST_FILENAME} !^.*\.(gif|jpg|jpeg|png|swf)$mtHi9).,y| RewriteRule .*$ - [F,L][ 3bwbfHhi &lt; /IfModule&gt;
