---
title: php中对共享内存，消息队列的操作
categories: []
date: 2013-01-16 23:22:54
---

php作为脚本程序，通常生命周期都很短，如在web应用中，一次请求就是php运行的一个周期，请求结束则生命周期截止。所以php在处理需要共 享的资源时，一般会将共享数据保存在数据库或dbm之类的文件中，再者就是利用内存实现共享。你可以选择已有的工具辅助你，像memcache；也可以自 己编写代码访问操作系统的共享内存段。

php中对共享内存段的操作有两组函数：System V IPC和Shared Memory。 其中System V IPC系列函数能够更方便的操作数据，无需像Shared Memory那样必须自己掌握读写时的偏移量、长度等，也不用序列化/反序列化来回转换（因为Shared Memory函数只支持字符串格式的数据参数）。但是System V IPC系列不支持Windows，所以如果要在win环境下使用，只能选Shared Memory。

因为php默认不支持这些函数，所以需要重编译php。如要使用：
System V信号量，编译时加上 –enable-sysvsem
System V共享内存，编译时加上 –enable-sysvshm
System V消息队列，编译时加上 –enable-sysvmsg
Shared Memory，编译时加上 –enable-shmop

先写个Shared Memory的例子：

```
<?php
$key = ftok(__FILE__, 'i');
$size = 100;
$shm_h = @shmop_open($key, 'c', 0644, $size);
if($shm_h === false) {
        echo "shmop open failed";
        exit;
}
$data = shmop_read($shm_h, 0, $size);
$data = unserialize($data);
//如果没有数据则写一个
if(empty($data)) {
        echo "there is no data";
        $data = "imdonkey";
        //就算数据是文本，write时也要序列化
        $write_size = shmop_write($shm_h, serialize($data), 0);
        if($write_size === false) echo "shmop write failed!";
}
//如果有，显示出来，之后删掉
else {
        echo "shared memory data: ";
        print_r($data);
        shmop_delete($shm_h);
}
shmop_close($shm_h);
?>

再写个System V shm的例子：
<?php 
$shm_key = ftok(__FILE__, 'i');
$memsize = 120;
$shm_h = shm_attach($shm_key, $memsize, 0644);
if($shm_h === false) {
        echo "shmop open failed";
        exit;
}
$var_key = 3;
$data = @shm_get_var($shm_h, $var_key);
if(empty($data)) {
        $data = "imdonkey";
        echo "there is no data, insert $data.\n";
        shm_put_var($shm_h, $var_key, $data);
} else {
        echo "find data: $data\n";
        shm_remove_var($shm_h, $var_key);
}
shm_detach($shm_h);
?>
```

可以看到，sysV对于每个数据都另外设立了对应的var_key，这样在同一内存区域可以保存多个数据，而不用像shmop中那样再申请另外一个共享内存区域，还免除了序列化的干扰（虽然数据最终还是以序列化的形式保存，但不用开发者去手动实现）。

例子虽然简单，但也有一些需要注意的地方，不管是shm_attach还是shmop_open，所申请的内存的大小一定要满足后面数据的体积，这 个体积包括数据本身序列化后的长，还有php添加的少量header信息。php官方文档中有人提出了一种计算要申请的内存大小的公式，这个公式可以保证 所申请的内存足够存储一个指定的数据。公式如下：

```
//当shm_attach第一次被调用时，php向共享内存写入一个header
$shmHeaderSize = (PHP_INT_SIZE * 4) + 8;
//当shm_put_var调用时，php会在序列化后的数据前面，加一个header
$shmVarSize = (((strlen(serialize($foo))+ (4 * PHP_INT_SIZE)) /4 ) * 4 ) + 4;
$memsize = $shmHeaderSize + $shmVarSize;
```

这个公式是否适用于所有情况，我不敢说，所以我想最好还是在程序中，将准备放入共享内存的数据结构设计好，尽量保证数据大小在某一范围内。

还有就是为了防止共享内存被浪费，当数据无用时及时调用对应的remove方法释放资源。

介绍完共享内存再顺带提一下消息队列Message Queue（也是在System V IPC函数组中），消息队列似乎可以视为另一种共享内存，只是数据存储的方式有些不同。简单来说，就是每个key对应一个队列，每个队列可以保存多个数据，数据间按照先进先出的原则进行操作。php文档中的例子很好的介绍了各函数的应用：

```
<?php 
if ( sizeof($argv)<2 ) { 
        echo "Usage: $argv[0] stat|send|receive|remove msgType MSG [msg] \n\n" ; 
        echo "   EX: $argv[0] send 1 \"This is no 1\" \n" ; 
        echo "       $argv[0] receive ID \n" ; 
        echo "       $argv[0] stat \n" ; 
        echo "       $argv[0] remove \n" ; 
        exit; 
} 
 
$MSGKey = "123456" ; 
$seg = msg_get_queue($MSGKey) ; 
 
switch ( $argv[1] ) { 
    case "send": 
        msg_send($seg, $argv[2], $argv[3]); 
        echo "msg_send done...\n" ; 
        break; 
 
    case "receive": 
        $stat = msg_stat_queue( $seg ); 
        echo 'Messages in the queue: '.$stat['msg_qnum']."\n"; 
        if ( $stat['msg_qnum']>0 ) { 
            msg_receive($seg, $argv[2], $msgtype, 1024, $data, true, MSG_IPC_NOWAIT); 
            var_dump($msgtype); 
            var_dump($data); 
            echo "\n"; 
        } 
        else { 
            echo "No Msg...\n"; 
        } 
        break; 
 
    case "stat": 
      print_r( msg_stat_queue($seg) ); 
        break; 
 
    case "remove": 
        msg_remove_queue($seg); 
        break; 
} 
?>

```

消息队列中的数据同样受到大小的约束，具体约束范围可通过msg_stat_queue的msg_qbytes看到。这段代码唯一有点小改动的地方就在接受消息时，指定了MSG_IPC_NOWAIT，不然如果目标队列没有数据，默认会一直等待。

一般会用到共享内存或消息队列的情况，都会涉及到多线程/进程，或跨语言的数据传递。如果是php脚本/进程间共享数据，那只要小心点操作就没什么 问题。如果要求跨语言，那很可能遇到千奇百怪的问题，呵呵，我还没试过，但在网上看到别人发的苦水贴，以后有机会一定实验一下。

在调试共享内存、信号量、消息队列时，可以配合Linux系统命令观察数据存储情况及信号量、消息队列资源分配情况，如ipcs, ipcrm命令。

利用PHP操作Linux消息队列完成进程间通信
　　当我们开发的系统需要使用多进程方式运行时，进程间通信便成了至关重要的环节。消息队列（message queue）是Linux系统进程间通信的一种方式。

　　关于Linux系统进程通信的概念及实现可查看：http://www.ibm.com/developerworks/cn/linux/l-ipc/

　　关于Linux系统消息队列的概念及实现可查看：http://www.ibm.com/developerworks/cn/linux/l-ipc/part4/
　　PHP的sysvmsg模块是对Linux系统支持的System V IPC中的System V消息队列函数族的封装。我们需要利用sysvmsg模块提供的函数来进进程间通信。先来看一段示例代码_1：

```
<?php
 
$message_queue_key = ftok(__FILE__, 'a');
 
$message_queue = msg_get_queue($message_queue_key, 0666);
var_dump($message_queue);
 
$message_queue_status = msg_stat_queue($message_queue);
print_r($message_queue_status);
 
//向消息队列中写
msg_send($message_queue, 1, "Hello,World!");
 
$message_queue_status = msg_stat_queue($message_queue);
print_r($message_queue_status);
 
//从消息队列中读
msg_receive($message_queue, 0, $message_type, 1024, $message, true, MSG_IPC_NOWAIT);
print_r($message."\r\n");
 
msg_remove_queue($message_queue);
 
?>  
这段代码的运行结果如下：
resource(4) of type (sysvmsg queue)
Array
(
    [msg_perm.uid] => 1000
    [msg_perm.gid] => 1000
    [msg_perm.mode] => 438
    [msg_stime] => 0
    [msg_rtime] => 0
    [msg_ctime] => 1279849495
    [msg_qnum] => 0
    [msg_qbytes] => 16384
    [msg_lspid] => 0
    [msg_lrpid] => 0
)
Array
(
    [msg_perm.uid] => 1000
    [msg_perm.gid] => 1000
    [msg_perm.mode] => 438
    [msg_stime] => 1279849495
    [msg_rtime] => 0
    [msg_ctime] => 1279849495
    [msg_qnum] => 1
    [msg_qbytes] => 16384
    [msg_lspid] => 2184
    [msg_lrpid] => 0
)
Hello,World!
```


可以看到已成功从消息队列中读取“Hello,World!”字符串
　　下面列举一下示例代码中的主要函数：

ftok ( string $pathname , string $proj )
    手册上给出的解释是：Convert a pathname and a project identifier to a System V IPC key。这个函数返回的键值唯一对应linux系统中一个消息队列。在获得消息队列的引用之前都需要调用这个函数。
 
msg_get_queue ( int $key [, int $perms ] )
    msg_get_queue() 会根据传入的键值返回一个消息队列的引用。如果linux系统中没有消息队列与键值对应，msg_get_queue()将会创建一个新的消息队列。函数 的第二个参数需要传入一个int值，作为新创建的消息队列的权限值，默认为0666。这个权限值与linux命令chmod中使用的数值是同一个意思，因为在linux系统中一切皆是文件。
 
msg_send ( resource $queue , int $msgtype , mixed $message [, bool $serialize [, bool $blocking [, int &$errorcode ]]] )
    顾名思义，该函数用来向消息队列中写数据。
 
msg_stat_queue ( resource $queue )
    这个函数会返回消息队列的元数据。消息队列元数据中的信息很完整，包括了消息队列中待读取的消息数、最后读写队列的进程ID等。示例代码在第8行调用该函数返回的数组中队列中待读取的消息数msg_qnum值为0。
 
```
msg_receive ( resource $queue , int $desiredmsgtype , int &$msgtype , int $maxsize , mixed &$message [, bool$unserialize [, int $flags [, int &$errorcode ]]] )
```

    msg_receive用于读取消息队列中的数据。
 
msg_remove_queue ( resource $queue )
    msg_remove_queue用于销毁一个队列。
示例代码_1只是展示了PHP操作消息队列函数的应用。下面的代码具体描述了进程间通信的场景
```
<?php
 
$message_queue_key = ftok(__FILE__, 'a');
$message_queue = msg_get_queue($message_queue_key, 0666);
 
$pids = array();
for ($i = 0;  $i < 5; $i++) {
        //创建子进程
        $pids[$i] = pcntl_fork();
 
        if ($pids[$i]) {
                echo "No.$i child process was created, the pid is $pids[$i]\r\n";
        } elseif ($pids[$i] == 0) {
                $pid = posix_getpid();
                echo "process.$pid is writing now\r\n";
 
                msg_send($message_queue, 1, "this is process.$pid's data\r\n");
                posix_kill($pid, SIGTERM);
        }
}
 
do {
        msg_receive($message_queue, 0, $message_type, 1024, $message, true, MSG_IPC_NOWAIT);
        echo $message;
 
        //需要判断队列是否为空，如果为空就退出
        //break;
} while(true)
 
?>
运行结果为：
No.0 child process was created, the pid is 5249
No.1 child process was created, the pid is 5250
No.2 child process was created, the pid is 5251
No.3 child process was created, the pid is 5252
No.4 child process was created, the pid is 5253
process.5251 is writing now
this is process.5251's data
process.5253 is writing now
process.5252 is writing now
process.5250 is writing now
this is process.5253's data
this is process.5252's data
this is process.5250's data
process.5249 is writing now
this is process.5249's data
```

这段程序每次的运行结果都会不同，这正说明了多进程的异步性。从结果也能看出消息队列FIFO特性。

以上便是我研究的一点心得。接下来将会继续研究PHP利用信号、socket等进行进程间通信的方法。


转载自：http://www.cnblogs.com/fengwei/archive/2012/09/12/2682646.html
