---
date: 2013-06-20T14:04:36
title: 什么是JS事件冒泡?"
categories: js
---

什么是JS事件冒泡？：

在一个对象上触发某类事件（比如单击onclick事件），如果此对象定义了此事件的处理程序，那么此事件就会调用这个处理程序，如果没有定义此事件处理程序或者事件返回true，那么这个事件会向这个对象的父级对象传播，从里到外，直至它被处理（父级对象所有同类事件都将被激活），或者它到达了对象层次的最顶层，即document对象（有些浏览器是window）。  

如何来阻止Jquery事件冒泡？

 通过一个小例子来解释

```html
<html xmlns="http://www.w3.org/1999/xhtml">
<script src="jquery-1.3.2-vsdoc.js" type="text/javascript"></script>  
<body> 
<form id="form1" runat="server"> <div id="divOne" onclick="alert('我是最外层');"> 
  <div id="divTwo" onclick="alert('我是中间层！')"> <a id="hr_three" href="http://www.baidu.com" mce_href="http://www.baidu.com" onclick="alert('我是最里层！')">点击我</a> </div> </div> 
</form> 
</body> 
</html>  
```

比如上面这个页面，

分为三层：divOne是第外层，divTwo中间层，hr_three是最里层;

他们都有各自的click事件，最里层a标签还有href属性。

 

运行页面，点击“点击我”,会依次弹出：我是最里层---->我是中间层---->我是最外层

---->然后再链接到百度.

 

这就是事件冒泡，本来我只点击ID为hr_three的标签，但是确执行了三个alert操作。

事件冒泡过程（以标签ID表示）：hr_three----> divTwo----> divOne 。从最里层冒泡到最外层。

 

如何来阻止?


```js

1.event.stopPropagation(); 

 <script type="text/javascript">

        $(function() {
            $("#hr_three").click(function(event) {
                event.stopPropagation();
            });
        });
<script>

```

再点击“点击我”，会弹出：我是最里层，然后链接到百度

 

 

 2.return false;

如果头部加入的是以下代码

 ```js
<script type="text/javascript"> $(function() {$("#hr_three").click(function(event) { return false;});}); <script>
 ```

再点击“点击我”，会弹出：我是最里层，但不会执行链接到百度页面

 

由此可以看出：

1.event.stopPropagation(); 

   事件处理过程中，阻止了事件冒泡，但不会阻击默认行为（它就执行了超链接的跳转）



2.return false;

   事件处理过程中，阻止了事件冒泡，也阻止了默认行为（比如刚才它就没有执行超链接的跳转）

 

还有一种有冒泡有关的：

3.event.preventDefault(); 

   如果把它放在头部A标签的click事件中，点击“点击我”。

   会发现它依次弹出：我是最里层---->我是中间层---->我是最外层，但最后却没有跳转到百度

 

    它的作用是：事件处理过程中，不阻击事件冒泡，但阻击默认行为（它只执行所有弹框，却没有执行超链接跳转）

