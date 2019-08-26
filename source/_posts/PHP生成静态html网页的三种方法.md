---
date: 2013-07-18T16:32:30

title: PHP生成静态html网页的三种方法

---

第一种：利用模板。目前PHP的模板可以说是很多了，有功能强大的smarty，还有简单易用的smarttemplate等。它们每一种模板，都有一个获取输出内容的函数。我们生成静态页面的方法，就是利用了这个函数。用这个方法的优点是，代码比较清晰，可读性好。

PHP代码举例：
```
<?php
require('smarty/Smarty.class.php');
$t = new Smarty;
$t->assign("title","Hello World!");
$content = $t->fetch("templates/index.htm");
//这里的 fetch() 就是获取输出内容的函数,现在$content变量里面,就是要显示的内容了
$fp = fopen("archives/2005/05/19/0001.html", "w");
fwrite($fp, $content);
fclose($fp);
?>
```


第二种方法：利用ob系列的函数。这里用到的函数主要是 ob_start(), ob_end_flush(), ob_get_content(),其中ob_start()是打开浏览器缓冲区的意思，打开缓冲后，所有来自PHP程序的非文件头信息均不会发送，而是保存在内部缓冲区，直到你使用了ob_end_flush().而这里最重要的一个函数，就是ob_get_contents(),这个函数的作用是获取缓冲区的内容，相当于上面的那个fetch(),道理一样的。

PHP代码举例：
```
<?php
ob_start();
echo "Hello World!";
$content = ob_get_contents();//取得php页面输出的全部内容
$fp = fopen("archives/2005/05/19/0001.html", "w");
fwrite($fp, $content);
fclose($fp);
?>

```

方法三：下面转贴一个比较通用且好懂的

前言：

目前网络上好多网站的新闻发布系统都采用了动态服务器技术生成静态HTML的做法，这样做的好处是：一是能减轻其服务器的负担，二是因为生成了HTML静态页面，所以其网站被搜索引擎搜索到的机率更大一些。笔者的网站曾经使用PHP这一动态技术来构建新闻发布系统，其原理也就是应用了PHP生成HTML静态页面的技术，相关平台是 Windows XP Sp2+php4.32+mysql，因此，在这里，想简单地谈一下这种做法的思路。这篇文章适合于对PHP+MYSQL数据库操作，SQL语句以及网页设计有点基础的朋友，如果您是一个从头开始学的朋友，那么请先打好基础吧！到这里就不用往下看了。如果您都符合上述条件的话，那么恭喜您，请接着往下看。但是，在具体动手构建之前，您还要做好以下几点准备工作。

一、 具备本地调试PHP的功能

在WINDOWS XP操作系统下，笔者建议你可以到网上下载一个PHP+MYSQL+APHCHE的服务器套装，如华军软件园，去那里搜索一下就可以下载到。下载完后默认安装即可，这样您就具备了在本地测试PHP的功能了，省去了很多手动配置的麻烦，怎么样，简单吧，OK，这仅仅只是第一步。

二、 构思新闻发布系统所具备的功能

首页的新闻发布往往是通过后台更新的，后台的更新无非是由添加、编辑、删除数据等基本的功能所实现的。在这里，你可以使用网页设计软件来构建你的想要的后台界面，其功能的实现当然是要用到PHP喽。这一步建议您先想好新闻发布系统要有的功能。在这里，如何用PHP添加、编辑、删除数据就不再重复，因为重点是如何在这基础上生成静态技术。

三、 PHP生成HTML的技术原理。

哈哈。费话说了那么多，终于到了该讲的地方了。其实，这一原理并不复杂，综合地来讲的话，应该是PHP中一个替换数据语法的应用。OK，讲一个简单范例吧，一步一步地分析！相信聪明的你能看得明白的咯，仔细看好每一步即可，在这里，只是引导大家如何做而已，具体的可以实践一下！

（1）在MYSQL里新建一数据库，命名为 database （可自定义），新建一表，命名为 news（因为是新闻发布嘛，取个好记的名字即可，可自定义），然后建立这几个字段名：
id (自动递增，这是关键，类型：INT)
title (顾名思义，新闻标题，类型可取 TEXT)
content (新闻内容，类型可取 TEXT)
path (HTML文件路径，类型可取 TEXT)

（2）建立 conn.php
这是连接数据库的PHP文件，你可以把连接数据的语句单独放在这一文件里，以后多个需要连接数据库的文件直接引用这个文件即可。

（3）设计添加新闻的表格 add.form 简单的源代码如下:
```
<form method=”post” action=”add.php”> //提交至 add.php
新闻标题:<input type=”text” name=”title” size=”20”><br>
新闻内容:<textarea name=”content” cols=”10” rows=”25”></textarea><br>
<input type=”submit” name=”提交”>
</form>

<form method=”post” action=”add.php”> //提交至 add.php
新闻标题:<input type=”text” name=”title” size=”20”><br>
新闻内容:<textarea name=”content” cols=”10” rows=”25”></textarea><br>
<input type=”submit” name=”提交”>
</form>

```

（4）建立一个 HTML 的模板,另存为model.htm,和 add.php可以在同一目录下。
示例源代码:
<html>
<body>
此新闻的标题:{title}
此新闻的内容:{content}
</body>
</html>
{ }大括号内的内容即是要被替换的内容，整个静态模板的设计可以根据自己的思路，但{ }内被替换的内容必须包含在内，如上面的{title}，{content}；咔咔~简单地说，设计好一个很好看的新闻模板后，把要被替换的如{title},{content}等标记放到需要的地方就可以了撒。

（5）详解 add.php 源码

```
<?php
require_once(“conn.php”)； //引用conn.php，连接数据库
$title=$_POST[“title”];
$content=$_POST[“content”]; //获得表单变量

//以下建立一文本文档，其值自动计数
$countfile="count.txt";
if(!file_exists($countfile))
{
fopen($countfile,"w"); //如果此文件不存在，则自动建立一个
}
$fp=fopen($countfile,"r");
$num=fgets($fp,20);
$num=$num+1; //每次其值自动加一
fclose($fp);
$fp=fopen($countfile,"w");
fwrite($fp,$num); //更新其值
fclose($fp);

//利用上面自动计数的值获得HTML的路径$path
$houzui=”.html”;
$path=$num.$houzui;
//这样形成的路径是自动增长的，如1.html,2.html,3.html……….添加一条新闻便自动加上1

//以下用SQL语句添加数据至表 news

$sql=”insert into news (title,content,path) values (‘”.$title.”’,’”.$content.”’,’”.$path.”’)”;
$query=mysql_query($sql);

//以下为关键之处，把从表单获得的数据替换模板中的{title},{content}标记

$fp=fopen(“model.htm”,”r”) //只读打开模板
$str=fread($fp,filesize(“mode.htm”));//读取模板中内容
$str=str_replace(“{title}”,$title,$str);
$str=str_replace(“{content}”,$content,$str);//替换内容
fclose($fp);

$handle=fopen($path,”w”); //写入方式打开新闻路径
fwrite($handle,$str); //把刚才替换的内容写进生成的HTML文件
fclose($handle);

//收尾工作:
echo “<a href=$path target=_blank>查看刚才添加的新闻</a>”;

OK,整个生成HTML的示例源码就到这里，其关键是用了替换的方法。
$str=str_replace(“{被替换的内容}”,$替换的内容,$str);
```


因此，总结一下以上的做法：先设计好新闻模板，把需要被替换的内容用{ }放到模板中相应的位置，然后设计表单，再是最后的表单处理程序，把从表单中获取的变量替换模板中相应的内容即可，这样每次都会生成不同的HTML；如果需要修改HTML的内容也是一样的，获得修改后的表单内容后，先用 update 语句更新数据库，再重新替换一下模板中的内容即可；删除的话，先delete表中要删除的内容，再用unlink($path) 来删除HTML的物理文件即可。

来自：http://blog.csdn.net/yeqihong/article/details/2122464
