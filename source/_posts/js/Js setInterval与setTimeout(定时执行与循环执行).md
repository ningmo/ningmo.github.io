---
title: Js setInterval与setTimeout(定时执行与循环执行)
categories: js
tags: [js]
date: 2012-12-16 00:36:11
---

Document自带的方法： 
循环执行：var timeid = window.setInterval（“方法名或方法”，“延时”）;window.clearInterval(timeid); 
定时执行：var tmid = window.setTimeout(“方法名或方法”, “延时”);window.clearTimeout(tmid); 
举例说明： 
```
A.当要执行的方法中不需要参数时 
<script type=”text/javascript”> 
//循环执行，每隔3秒钟执行一次showalert（） 
window.setInterval(showalert, 3000); 
function showalert() 
{ 
alert(“aaaaa”); 
} 
//定时执行，5秒后执行show() 
window.setTimeout(show,5000); 
function show() 
{ 
alert(“bbb”); 
} 
</script> 
B.当要执行的方法中需要参数时 
复制代码代码如下:
<script type=”text/javascript”> 
//循环执行，每隔3秒钟执行一次 showalert（） 
window.setInterval(function(){ 
showalert(“aaaaa”); 
}, 3000); 
function showalert(mess) 
{ 
alert(mess); 
} 
//定时执 行，5秒后执行showalert() 
window.setTimeout(function(){ 
showalert(“bbbbbb”); 
},5000); 
</script>
```

