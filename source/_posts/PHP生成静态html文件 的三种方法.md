---
date: 2013-07-18T16:37:19

title: PHP生成静态html文件 的三种方法

---

1,下面使用模版的一个方法!
```
 <?php 
$fp = fopen ("templets.html","a"); 
if ($fp){ 
$fup = fread ($fp,filesize("templets.html")); 
$fp2 = fopen ("html.shtml","w"); 
if ($fwrite ($fp2,$fup)){ 
$fclose ($fp); 
$fcolse ($fp2); 
die ("写入模板成功"); 
} else { 
fclose ($fp); 
die ("写入模板失败!"); 
} 
} 
?> 
```

简单的将模板写进一个文件中存为html.html
2,按时间生成html文件名
 <? 
$content = "这是一个以日期时间为文件名的静态生成网页的测试文件，
文件名格式一般为<font color=#ff0000>年月日时分秒.html</font>"; 
```
$date = date('YmdHis'); 
$fp = fopen (date('YmdHis') . '.html',"w");
```

//本函数可用来打开本地或者远端的文件 'w' 开文件方式为写入，
文件指针指到开始处，并将原文件的长度设为 0。若文件不存在，
则建立新文件。 
```
if (fwrite ($fp,$content)){
//格式是.int fwrite(int fp(文件名), string string(内容),
 int [length](长度));本函数将字符串 string 写入文件资料流的指针 fp 上。
若有指定长度 length，则会写入指定长度字符串，或是写到字符串结束。 
fclose ($fp);//函数用来关闭已经打开的文件的指针 fp。
成功返回 true，失败则返回 false。 
die ("写入模板成功"); 
} else { 
fclose ($fp); 
die ("写入模板失败!"); 
} 
echo ($content); 
?>
```

3,下面为转换文件名的一个方法
 ```
<?php 
$s_fname = "93e.php"; 
$o_fname = "93e.htm"; 
ob_end_clean(); 
ob_start(); 
include($s_fname); 
$length = ob_get_length(); 
$buffer = ob_get_contents(); 
$buffer = eregi_replace("r","",$buffer); 
ob_end_clean(); 

$fp = fopen($o_fname,"w+"); 
fwrite($fp,$buffer); 
fclose($fp); 
?>
```

这样就可以把 93e.php转化为静态的HTML文件了 。要注意的是待转换的文件里不能有 ob_end_clean();和 ob_start();语句，且目录要有写权限。

来自：http://www.phpchina.com/archives/view-36910-1.html
