---
date: 2013-07-25T10:48:10

title: PHP执行外部命令

---

PHP是完全支持外部命令的，但是出于安全考虑，一般很少使用。

PHP提供共了3种方法调用外部命令：

（1）调用执行外部命令函数（system()，exec()，passthru()，shell_exec()）

（2）使用反撇号`执行命令

（3）popen()函数打开进程

下面来一一说明：

一、调用执行外部命令函数

1.exec()函数  ```
string exec ( string $command [, array &$output [, int &$return_var ］ )
```


说明：这个函数不会直接输出结果，而是把结果输出到第二个参数（数组）中，第三个参数用来取得命令执行的状态码，通常执行成功都是返回0。


```
<?php
	//打印出当前文件目录及其文件信息
	exec("dir",$out);
	print_r($out);
?>
```


2.system()函数  string system ( string $command [, int &$return_var ] )

 说明：

system在执行系统外部命令时，它执行给定的命令，输出和返回结果。第二个参数是可选的，用来得到命令执行后的状态码。


```
<?php
    system("dir");
?>

```

3.passthru()函数 void passthru ( string $command [, int &$return_var ] )

说明：passthru直接将结果输出到游览器，不返回任何值，且其可以输出二进制，比如图像数据。第二个参数可选，是状态码。


```
<?php
 passthru ('echo $PATH');
 ?>
```


4.shell_exec()函数 string shell_exec ( string $cmd )


```
<?php
/** by www.phpddt.com */
$output = shell_exec('ls -l');
var_dump($output);
?> 
```


二、使用反撇号`执行命令


```
<?php
	echo `dir`;
?> 

```

说明: 在使用这种方法执行系统外部命令时，要确保shell_exec函数可用，否则是无法使用这种反撇号执行系统外部命令的。

三、使用popen()打开进程，执行命令

```
resource popen ( string $command , string $mode )
```


说明：可以进行命令交互，popen( )函数打开一个进程管道来执行给定的命令，返回一个文件句柄，可以对它读和写。返回值和fopen()函数一样，返回一个文件指针。除非使用的是单一的模式打开(读or写)，否则必须使用pclose()函数关闭。该指针可以被fgets(),fgetss(),fwrite()调用。出错时，返回FALSE。

 可以借鉴手册上这个例子：


```
<?php
error_reporting(E_ALL);
$handle = popen('/path/to/executable 2>&1', 'r');
echo "'$handle'; " . gettype($handle) . "\n";
$read = fread($handle, 2096);
echo $read;
pclose($handle);
?> 
```


在PHP中你这样做，可能会带来安全隐患的（详解PHP漏洞），当你使用这些函数来执行系统命令时，可以使用escapeshellcmd()和escapeshellarg()函数阻止用户恶意在系统上执行命令，escapeshellcmd()针对的是执行的系统命令，而escapeshellarg()针对的是执行系统命令的参数。这两个参数有点类似addslashes()的功能。

From:http://www.phpddt.com/php/php-execute-an-external-program.html
