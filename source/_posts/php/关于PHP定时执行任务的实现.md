---
title: 关于PHP定时执行任务的实现
categories: [php]
date: 2013-07-25 12:34:41
---

PHP在这方面应该说是比较弱，如果只用php去实现可以如下：
```<?php
	ignore_user_abort();//关闭浏览器后，继续执行php代码
	set_time_limit(0);//程序执行时间无限制
	$sleep_time = 1;//多长时间执行一次
	do{
		$fp = fopen('test.txt','a+');
		fwrite($fp,"这是一个php博客：phpddt.com \n");
		fclose($fp);
		sleep($sleep_time);
	}while(true);
	
?>```
但是当我执行脚本的时候，即使我关闭了浏览器，我根本就没法去停止这段程序了，所以你需要一个执行脚本的开关，你可以用外部文件引入的方法来实现，在while循环的时候，include开关变量即可。那么就可以这样实现： 建立外部引入变量文件 switch.php 内容如下：
​```<?php

return 1;//1执行脚本 0退出执行脚本

?>```
改良脚本如下：
​```<?php
	ignore_user_abort();//关闭浏览器后，继续执行php代码
	set_time_limit(0);//程序执行时间无限制
	$sleep_time = 5;//多长时间执行一次
	$switch = include 'switch.php';
	while($switch){
		$switch = include 'switch.php';
		$fp = fopen('test.txt','a+');
		fwrite($fp,"这是一个php博客：phpddt.com $switch \n");
		fclose($fp);
		sleep($sleep_time);
	}
	exit();
	
?>```
这个脚本只是测试可行，具体效率应该不高，对于lamp,你完全可以使用crontab 来实现
From:http://www.phpddt.com/php/php-timed-task.html

```