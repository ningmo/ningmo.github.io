---
title: js模拟点击事件
categories: []
date: 2012-12-16 00:34:31
---

在实际的应用开发中，我们会常常用到JS的模事件，比如说点击事件，举个简单的例子，点击表单外的“提交”按钮来提交表单。上代码吧。
```
<h3>请单击“提交”，测试提交按钮的单击事件也被触发了。</h3> <button id="btn">提交</button> <form action="#" method="get" id="form">   <input type="text" name="site" value="www.woiweb.net" readonly/>   <input id="subbtn" type="submit" value="先别点击此按钮提交" onclick="alert('我已经提交了');"/> </form>   Javscript: <script type="text/javascript">   var sub = document.getElementById("subbtn");   var btn = document.getElementById("btn"); //通用方法 btn.onclick = function() { sub.click(); } </script>
```
经过测试，IE，FF，Chrome，Opera，Safari都没有问题，均可正常提交表单。

但在实际的设计中，为了让提交按钮更好看，buildder经常把它们用a标签来处理，加个背景图片来模拟按钮，我们仍然用上面的思路来尝试，增加一个a标签，让它来提交表单，我们仅修改html。


```

<h3>请单击“提交”，测试提交按钮的单击事件也被触发了。</h3> <button id="btn">提交</button> <form action="#" method="get" id="form">   <input type="text" name="site" value="www.woiweb.net" readonly/>   <!--<input id="subbtn" type="submit" value="先别点击此按钮提交" onclick="alert('我已经提交了');"/> -->   <a id="subbtn" href="javascript:;" onclick="alert('在此调用提交表单的方法')">模拟提交按钮</a> </form>   javascript:   <script type="text/javascript">   var sub = document.getElementById("subbtn");   var btn = document.getElementById("btn"); //通用方法 btn.onclick = function() { sub.click(); } 8 </script>

```


运行后，问题出现了，IE、FF、Opera均OK，但Chrome和Safari不能正常运行，后来网上搜索了下，发现a标签并不是和按钮一样有onclick()事件的，解决办法是针对 IE 和 FF编写不同的逻辑，JS代码如下：
```

<script type="text/javascript">   var sub = document.getElementById("subbtn");   var btn = document.getElementById("btn"); //通用方法 btn.onclick = function() {    //sub.click();    if (/msie/i.test(navigator.userAgent)) //IE    {       sub.fireEvent("onclick");    } else {       var e = document.createEvent('MouseEvent');       e.initEvent('click', false, false);       sub.dispatchEvent(e);    }   } </script>


```



至此，问题解决，虽然这个问题很简单，但很容易被大家忽略，贴出来和大家一起分享。

语法： 
createEvent(eventType)

参数 描述
eventType 想获取的 Event 对象的事件模块名。关于有效的事件类型列表，请参阅”说明”部分。
返回值

返回新创建的 Event 对象，具有指定的类型。

抛出

如果实现支持需要的事件类型，该方法将抛出代码为 NOT_SUPPORTED_ERR 的 DOMException 异常。

说明

该方法将创建一种新的事件类型，该类型由参数 eventType 指定。注意，该参数的值不是要创建的事件接口的名称，而是定义那个接口的 DOM 模块的名称。

下表列出了 eventType 的合法值和每个值创建的事件接口：
```

参数 事件接口 初始化方法
HTMLEvents HTMLEvent iniEvent()
MouseEvents MouseEvent iniMouseEvent()
UIEvents UIEvent iniUIEvent()
```

用该方法创建了 Event 对象以后，必须用上表中所示的初始化方法初始化对象。关于初始化方法的详细信息，请参阅 Event 对象参考。

该方法实际上不是由 Document 接口定义的，而是由 DocumentEvent 接口定义的。如果一个实现支持 Event 模块，那么 Document 对象就会实现 DocumentEvent 接口并支持该方法。

```
