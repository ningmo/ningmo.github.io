---
date: 2013-07-25T11:23:11
title: 对PHP输入输出流学习和认识
categories: php
---

PHP输入和输出流是通过php://来访问的，它允许访问 PHP 的输入输出流、标准输入输出和错误描述符， 内存中、磁盘备份的临时文件流以及可以操作其他读取写入文件资源的过滤器。

php://stdin, php://stdout 和 php://stderr

php://stdin，php://stdout 和 php://stderr允许访问 PHP 进程相应的输入或者输出流。

php://input

php://input 是个可以访问请求的原始数据的只读流。 POST 请求的情况下，最好使用 php://input 来代替 $HTTP_RAW_POST_DATA（原生的post数据），因为它不依赖于特定的 php.ini 指令,内存消耗更少。如下例：


```
<form action="" method="post">
    <input type="text" name="test" > <input type="submit" name="" />
</form>
<?php
    echo file_get_contents("php://input");
?>

```

结果：

php://input接受post数据

php://output

php://output 是一个只写的数据流， 允许你以 print 和 echo 一样的方式 写入到输出缓冲区。

php://fd

php://fd 允许直接访问指定的文件描述符。 例如 php://fd/3 引用了文件描述符 3。

php://memory 和 php://temp

php://memory 和 php://temp 是一个类似文件 包装器的数据流，允许读写临时数据。 两者的唯一区别是 php://memory 总是把数据储存在内存中， 而 php://temp 会在内存量达到预定义的限制后（默认是 2MB）存入临时文件中。 临时文件位置的决定和 sys_get_temp_dir() 的方式一致。

php://filter

php://filter 是一种元封装器， 设计用于数据流打开时的筛选过滤应用。 这对于一体式（all-in-one）的文件函数非常有用，类似 readfile()、 file() 和 file_get_contents()， 在数据流内容读取之前没有机会应用其他过滤器。参数如下：

php:filter//过滤器参数

如下例：


```
<?php
/* 这会以大写字母输出 www.phpddt.com 的全部内容 */
readfile("php://filter/read=string.toupper/resource=http://www.phpddt.com");
?>
```
以上摘自：http://www.phpddt.com/php/php-input-output.html
以下出自php手册

Example #1 php://temp/maxmemory


 This optional parameter allows setting the memory limit before php://temp starts using a temporary file. 


```
<?php
// Set the limit to 5 MB.
$fiveMBs = 5 * 1024 * 1024;
$fp = fopen("php://temp/maxmemory:$fiveMBs", 'r+');

fputs($fp, "hello\n");

// Read what we have written.
rewind($fp);
echo stream_get_contents($fp);
?> 
```



Example #2 php://filter/resource=<stream to be filtered>


 This parameter must be located at the end of your php://filter specification and should point to the stream which you want filtered. 


```
<?php
/* This is equivalent to simply:
  readfile("http://www.example.com");
  since no filters are actually specified */

readfile("php://filter/resource=http://www.example.com");
?> 

```


Example #3 php://filter/read=<filter list to apply to read chain>


 This parameter takes one or more filternames separated by the pipe character |.


```
<?php
/* This will output the contents of
  www.example.com entirely in uppercase */
readfile("php://filter/read=string.toupper/resource=http://www.example.com");

/* This will do the same as above
  but will also ROT13 encode it */
readfile("php://filter/read=string.toupper|string.rot13/resource=http://www.example.com");
?> 

```


Example #4 php://filter/write=<filter list to apply to write chain>


 This parameter takes one or more filternames separated by the pipe character |.


```
<?php
/* This will filter the string "Hello World"
  through the rot13 filter, then write to
  example.txt in the current directory */
file_put_contents("php://filter/write=string.rot13/resource=example.txt","Hello World");
?> 
```

