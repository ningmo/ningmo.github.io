---
title: javascript urlencode 推荐 encodeURIComponent
categories: js
date: 2012-12-18 23:06:16
tags: [js]
---

网上查到的全都是escape，和需要的编码不是一回事，好不容易找到的结果 保存下来以备以后使用
js对文字进行编码涉及3个函数：escape,encodeURI,encodeURIComponent，相应3个解码函数：unescape,decodeURI,decodeURIComponent
1、   传递参数时需要使用encodeURIComponent，这样组合的url才不会被#等特殊字符截断。                            
例如：
```
<script language="javascript">document.write('<a href="http://passport.baidu.com/?logout&aid=7&u='+encodeURIComponent("http://cang.baidu.com/bruce42")+'">退出</a>');</script>
```

2、   进行url跳转时可以整体使用encodeURI
例如：```
Location.href="/encodeURI"("http://cang.baidu.com/do/s?word=百度&ct=21");
```

3、   js使用数据时可以使用escape
例如：搜藏中history纪录。
4、   escape对0-255以外的unicode值进行编码时输出%u****格式，其它情况下escape，encodeURI，encodeURIComponent编码结果相同。
最多使用的应为encodeURIComponent，它是将中文、韩文等特殊字符转换成utf-8格式的url编码，所以如果给后台传递参数需要使用encodeURIComponent时需要后台解码对utf-8支持（form中的编码方

```