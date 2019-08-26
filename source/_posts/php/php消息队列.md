---
title: php消息队列
categories: []
date: 2013-01-23 14:31:09
---

首先既然是队列当然满足FIFO的性质。称它为消息队列是因为它是在多个进程中共享消息的机制。当然System V下还有信号量和共享内存等机制来完成进程间内容的共享。但是队列这个结构的性质非常适合完成系列连贯的任务，而且任务处理又有先后顺序的情形。
PHP中用于消息队列的函数如下：
```
int ftok ( string $pathname , string $proj ) //负责创建消息队列的key
resource msg_get_queue ( int $key [, int $perms ] ) //通过key来创建消息队列
bool msg_queue_exists ( int $key ) //判断队列是否存在
bool msg_remove_queue ( resource $queue ) //删除消息队列
array msg_stat_queue ( resource $queue ) //获得队列状态信息，包括队列中还有多少消息未读等信息。
bool msg_send ( resource $queue , int $msgtype , mixed $message [, bool $serialize [, bool $blocking [, int &$errorcode ]]] ) //发送消息至消息队列
bool msg_receive ( resource $queue , int $desiredmsgtype , int &$msgtype , int $maxsize , mixed &$message [, bool $unserialize [, int $flags [, int &$errorcode ]]] ) //从消息队列中获取消息
```

下面我来具体说说msg_send和msg_receive，因为其他的函数使用起来都比较简单，看一下文档就可以了。
msg_send在发送消息的时候可以指定可以msgtype的参数，这个参数是用来指定消息类型的，参数类型为数字型。使用这个参数我们可以在同一个消息队列中放置不同类型的消息，从而不必开辟多个消息队列。serialize参数来指定是否需要将消息变量进行序列化，序列化后我们就可以存放结构更为复杂的变量。而blocking参数用来指定在队列满后msg_send的动作，当blocking为true时，msg_send会挂起，等待消息队列中有足够的空间放置消息时再次发送消息。而如果blocking为false则在发现队列满后不执行send直接结束。
msg_receive可以指定你需要从队列中取回的消息类型,即desiremsgtype，它会从队列中取出第一个满足此类型的消息。如果desiremsgtype为0，则不考虑类型，取出消息队列的第一个元素。它的值也可以为负值，此时返回的数据类型为小于此负值绝对值的最小值。例如你指定-5为希望的类型，那么如果有类型为1的消息则优先返回。若队列中没有符合的类型可以返回的话，此函数挂起等待相应类型加入队列后将其取出，若flag中有MSG_IPC_NOWAIT的话则立即返回。msgtype为一个引用传递参数，获取实际获得的消息类型。maxsize指定消息的最大长度，若消息长度超过最大长度则产生错误，若指定flag为MSG_NOERROR则消息会根据最大长度截取，并抑制错误的产生。unserialize用来指定是否需要解序列化。flag中还有一个标记是MSG_EXCEPT，通过配合desiremsgtype可以实现选择不是某种类型的消息。
下面我们讨论如何通过消息队列实现PHP的多进程。
因为消息队列是系统的公共存储空间，因此所有的php进程都可以访问。最简单的实现方法就是通过一个php将要处理的任务发送到消息队列中，然后通过开启子进程或者开启其他的php脚本来从消息队列中取出任务进行执行。
开启进程的方法有好多，可以使用pcntl_fork来创建一个子进程，也可以再找一个php脚本来处理，通过pcntl_exec来启动。或者根本不管进程空间，直接exec()来开启一个php脚本来处理。
我使用的方法是通过计数来开启其他php脚本处理。通过一个php取出要处理的任务，将其压入消息队列，每压入指定条数的消息就开启另一个php来处理消息队列的内容，直到所有要处理的任务都压入消息队列，主php结束执行。此时后台会有好多php脚本在执行消息队列中的任务，当消息队列为空则通过开启的php脚本将消息队列撤销，同时所有开启的php脚本结束。整个执行过程完成。
当然我们也可以通过pcntl_fork函数将程序写的更简单一些，在主进程中将任务入队，每隔一定的任务数开启一个当前进程的子进程来处理入队的任务，直到消息队列为空时结束。
