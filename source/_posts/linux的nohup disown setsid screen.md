---
date: 2015-12-15T23:46:43

title: linux的nohup disown setsid screen

---

<pre class="prettyprint">nohup 可以将进程挂起 帐号退出时不受影响
    nohup cp -r a b/ [>/~/cp.log] &  
setsid 作用类似于nohup
    swtsid cp -r a b/
disown 使已经在运行的用户进程 不受用户退出限制
    disown -h %1  # 1 为jobsID 
    如果已经在当前终端运行 
        可以ctrl+z 挂起 
        bg jobsID 放入后台
        disown -h jobsID 

screen 适用于多条命令

jobsID 可以通过jobs命令显示```
