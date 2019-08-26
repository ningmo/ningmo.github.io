---
title: php定时执行任务的几个方法
categories: []
date: 2013-01-23 14:45:16
---

PHP的实现决定了它没有Java和.Net这种AppServer的概念, 而http协议是一个无状态的协议, php只能被用户触发, 被调用, 调用后会自动退出内存, 没有常驻内存, 就没有办法准确的定时处理
那么, 如果需要用PHP定时执行某些任务的话, 可以有以下俩个方法:
Linux下crontab, windows下计划任务
找个被频繁调用的网页, 里面加上一个检测代码
set_time_limit(0);
ignore_user_abort(true);
死循环 第一个方法是最常见的, 如果php服务器上没有权限去crontab, 也可以找一个自己的机器定期crontab去请求服务器
第二种方法, 论坛上定时清理新帖基本上就是这么实现的, 如果人气不旺的话, 可以考虑去别的火爆点的论坛里面发个图片, 调用自己的php来实现一个trigger就好了。 Discuz!是判断在00:00之后,第一个执行者去执行的。
第三种比较不靠谱, Apache重启了就得重新访问,  fastcgi倒是会好一点。
下面的这段代码，可以在当前文件夹下，生成一个test.txt，并每隔20秒，往里面写入一个时间戳，无论客户端是否关闭浏览器。
```
<?php
ignore_user_abort(true);
set_time_limit(0);
function write_txt(){
if(!file_exists(”test.txt”)){
$fp = fopen(”test.txt”,”wb”);
fclose($fp);
}
$str = file_get_contents(’test.txt’);
$str .= “\r\n”.date(”H:i:s”);
$fp = fopen(”test.txt”,”wb”);
fwrite($fp,$str);
fclose($fp);
}
function do_cron(){
usleep(20000000);
write_txt();
}
while(1){
do_cron();
}
```

关键的两个函数：
ignore_user_abort(true)，这个函数的作用是，无论客户端是否关闭浏览器，下面的代码都将得到执行。
set_time_limit(0)，这个函数的作用是，取消php文件的执行时间，如果没有这个函数的话，默认php的执行时间是30秒，也就是说30秒后，这个文件就say goodbay了。
另外usleep在PHP5.0之后，支持windows操作系统。
我们在做一个php 邮件发送问题是常常会遇见这样的问题，就是用户订阅一些资料需要定时发送到用户的邮箱中去。去网上搜索了一下，发现在这样的文章不多，本文介绍了一种用PHP实现的方法，笔者用PHP的时间不长，欢迎大家PP。

　　一、要实现定时发送，主要解决问题是定时。

　　我们在写程序时需要加个什么if 某个时间＝某个时间则发送，可是要实现这个过程，面临的问题是，我们要执行这个页面才能发送。所以主要解决的问题是怎么到时让服务器定时执行这个页面，这样实现起来好像比较困难。

　　二、我翻开PHP手册，找到了PHP 的命令行模式这一章，发现能解决这一问题，建议大家如果想用这个方法的话先看看这一章。
　三、解决方法：
1、在 Windows 平台下您可以将cli\php.exe 和 .php 文件的双击属性相关联，您也可以编写一个批处理文件来用 PHP 执行脚本。我们把写好的程序放在一个目录下如　E:\web\mail.php
然后写一个windows批处理文件内容如下。
@D:\php\cli\php.exe E:\web\mail.php >d:\php\cli\sendmail.log
Pause
那个D:\php\cli\php.exe是我的PHP安装文件所在目录。Php.exe就是windows　PHP命令行模式的程序。
好的，我们保存这个文件为mail.bat然后的windows中的计划任务中添加一个任务，让操作系统在某个时间来运行这个批处理文件。
2、如果您使用 Unix 系统，您需要在您的 PHP 脚本的最前面加上一行特殊的代码，使得它能够被执行，这样系统就能知道用什么样的程序要运行该脚本。为 Unix 系统增加的第一行代码不会影响该脚本在 Windows 下的运行，因此您也可以用该方法编　　　写跨平台的脚本程序。以下是一个简单的PHP 命令行程序的范例。
　　四、例子：
E:\web \Timesend.php
#!/usr/bin/php
```
<?php
    require_once("E:\web\includes\config.php");            
    require_once("E:\web\includes\class\mail.class.php");
       require_once("E:\web\includes\class\smtp.class.php");
       // +----------------------------------------------------+
//数据库配置
    $dbhost = "localhost";
    $dbport = "3306";
    $dbname = "";
    $dbuser = "";
    $dbpawd = "";
// +----------------------------------------------------+
//数据库连接对象
    $db = new dbLink($dbhost,$dbport,$dbuser,$dbpawd,$dbname);
       $query = "SELECT * FROM wl_mailtemplate WHERE mt_name = 'UserUpdate'";
       $mailtemplate =$db->dbQuery($query);
       $username = 'sdfsdfdsd';
    $sex = "先生";
       $accounts = "sdfasdfasdfsad";
       $password = "sdfsadfsdasdasddssfds";
 
 
       $message = "
 
                                   $message = addslashes($message);
                                   eval_r("\$message = \"$message\";");
                                   
       $mail = new SendMail('wfits@126.com', $mailtemplate[0]['mt_subject'], nl2br($message));
       if ($mail->Send())
       {
 
 
       $feedback = "修改确认信息已发送到您的注册Email，当前登录已被注销。\\n请注意查收确认信，并取得新的登录密码。";
        echo $feedback;
        }
?>
```

写个bat文件。
@D:\php\cli\php.exe E:\web\mail.php >d:\php\cli\sendmail.log
Pause
保存为：timesend.bat放在@D:\php\cli\php.exe目录下
在window中添加计划任务，然后就可以啦！

　　五、说明。
　1、  我发送邮件用的是模板在数据库中存着，还有两个邮件发送类就不提供了，要的话可以联系我。
　2、  在使用requrie_once时要用绝对路径。
　3、  PHP 的命令行模式能使得 PHP 脚本能完全独立于 WEB 服务器单独运行，所以要是大量发送邮件时就能减轻服务器负担。
　4、  再一次建议大家看看PHP手册　第23. 章PHP 的命令行模式。
　
     其实这不是真正的实现邮件自动发送的方法，但是在无桌面应用程序中的ＷＥＢ方式下，这可能是一个比较好的方式吧～！，我想那种真正实现邮件自动发送的系统，在服务器端还是有一个桌面应用程序做支撑的！所以说这种实现邮件的自动发送仅仅是实现ＰＨＰ程序发送邮件的一种方法！
 
 
```
 
" . $mailtemplate[0]['mt_message']. "
";
<?php
ignore_user_abort();           // 即使Client断开(如关掉浏览器)，PHP脚本也可以继续执行.
set_time_limit(0);             // 执行时间为无限制，php默认的执行时间是30秒，通过set_time_limit(0)可以让程序无限制的执行下去
$interval=20;               // 时间间隔 单位 秒
$key_file="key.txt";          // 配置文件

if (isset($_GET['s']))
{
  if ($_GET['s']=="0"){        // 停止工作，但不退出
    $s="false";
    echo "Function is off";
  }
  elseif ($_GET['s']=="1"){      // 工作
    $s="true";
    echo "Function is on";
  }
  elseif ($_GET['s']=="2"){      // 退出
    $s="die";
    echo "Function exited";
  }
  else
    die("Err 0:stop working 1:working 2:exit");
  
  $string = "<?php\n return \"".$s."\";\n?>";
  write_inc($key_file,$string,true);
  exit();
}

if(file_exists($key_file)){
  do{
    $mkey = include $key_file;
    if ($mkey=="true"){          // 如果工作
    /////////////////////  工作区间  //////////////////////////////////
      $showtime=date("Y-m-d H:i:s");
      $fp = fopen('func.txt','a');
      fwrite($fp,$showtime."\n");
      fclose($fp);
    ///////////////////////////////////////////////////////////////////
    }
    elseif ($mkey=="die"){        // 如果退出
      die("I am dying!");
    }
    sleep($interval);           // 等待$interval分钟
  }while(true);
}
else
  die($key_file." doesn't exist !");

function write_inc($path,$strings,$type=false)
{  
  $path=dirname(__FILE__)."/".$path;
  if ($type==false)
    file_put_contents($path,$strings,FILE_APPEND);
  else
    file_put_contents($path,$strings);
}

?>
```



原文:http://blbear.com/post/187
