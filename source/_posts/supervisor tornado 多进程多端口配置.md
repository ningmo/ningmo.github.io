---
date: 2015-11-24T16:42:09

title: supervisor tornado 多进程多端口配置

---

base： nginx tornado 
目标： tornado 实现多端口多进程运行

pip install supervisor
echo_supervisord_conf > /etc/supervisord.conf   # 默认echo_supervisord_conf 在python/bin 目录下 
配置/etc/supervisord.conf

```
<code>[program:app]
command=python /data/www/app/app.py --port=600%(process_num)d
process_name=%(program_name)s%(process_num)d
numprocs=6         ; 开启6个子进程
numprocs_start=1   ; 上面的process_num从1开始  
directory=/data/www/app
autostart=true
autorestart=false
;startsecs=5                   ; number of secs prog must stay running (def. 1)
startretries=3                ; max # of serial start failures (default 3)
;stopsignal=QUIT               ; signal used to kill process (default TERM)
;stopwaitsecs=10               ; max num secs to wait b4 SIGKILL (default 10)
;stopasgroup=false             ; send stop signal to the UNIX process group (default false)
;killasgroup=false             ; SIGKILL the UNIX process group (def false)
user=root                   ; setuid to this UNIX account to run the program
redirect_stderr=true          ; redirect proc stderr to stdout (default false)
stdout_logfile=/data/logs/supervisor/order.log        ; stdout log path, NONE for none; default AUTO
stderr_logfile=/data/logs/supervisor/order_error.log        ; stderr log path, NONE for none; default AUTO
;serverurl=AUTO                ; override serverurl computation (childutils)</code>
```





