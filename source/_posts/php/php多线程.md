---
title: php多线程
categories: [php]
date: 2013-01-31 10:33:10
---

<code>```
<?php
error_reporting(E_ALL & ~E_NOTICE);
/**
 * @title:		PHP多线程类(Thread)
 * @version:	1.0
 * @author:		phper.org.cn < web@phper.org.cn >
 * @published:	2010-11-2
 * 
 * PHP多线程应用示例：
 *  require_once 'thread.class.php';
 *  $thread = new thread();
 *  $thread->addthread('action_log','a');
 *  $thread->addthread('action_log','b');
 *  $thread->addthread('action_log','c');
 *  $thread->runthread();
 *  
 *  function action_log($info) {
 *  	$log = 'log/' . microtime() . '.log';
 *  	$txt = $info . "\r\n\r\n" . 'Set in ' . Date('h:i:s', time()) . (double)microtime() . "\r\n";
 *  	$fp = fopen($log, 'w');
 *  	fwrite($fp, $txt);
 *  	fclose($fp);
 *  }
 */
class thread {
	
    var $hooks = array();
    var $args = array();
    #var $address='';

    function thread() {
		#$this->address=$address;
    }
    
    function addthread($func)
    {
    	$args = array_slice(func_get_args(), 1);
    	$this->hooks[] = $func;
		$this->args[] = $args;
		return true;
    }
    
    function runthread()
    {
    	if(isset($_GET['flag']))
    	{
    		$flag = intval($_GET['flag']);
    	}
    	if($flag || $flag === 0)
		{
			call_user_func_array($this->hooks[$flag], $this->args[$flag]);
		}
    	else 
    	{
        	for($i = 0, $size = count($this->hooks); $i < $size; $i++)
        	{
				/*
				if($this->address)
				{
					$addr=$this->address;
				}
				else
				{
					$addr=$_SERVER['HTTP_HOST'];
				}
				*/
        		$fp=fsockopen($_SERVER['HTTP_HOST'],$_SERVER['SERVER_PORT']);
				if($fp)
        		{
        			$out = "GET {$_SERVER['PHP_SELF']}?flag=$i HTTP/1.1\r\n";
        			$out .= "Host: {$_SERVER['HTTP_HOST']}\r\n";
        			$out .= "Connection: Close\r\n\r\n";
					fputs($fp,$out);
	                fclose($fp);
        		}
        	}
    	}
    }
}
```
</code>
