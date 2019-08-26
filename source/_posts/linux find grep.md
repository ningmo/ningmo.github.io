---
date: 2015-10-31T10:51:01

title: linux find grep

---

```
在当前目录下所有文件中查找内容包含 string 的文件:
find ./ -name "*" -exec grep "string" {} \;
注意:在最后不能加 print ,否则会出错.
 
在当前目录下所有文件中查找内容包含 string 的文件并列出字符所在的文件:
find ./ -name "*" -exec grep -l "string" {} \;
 
在当前目录下 *.c 中查找内容包含 string 的文件并列出字符所在的文件的所在行(不显示文件名):
find ./ -name "*.c" -exec grep -n "string" {} \;
 
在当前目录下所有文件中查找内容包含 string 的文件并列出字符所在的文件,所在行及所在行的内容:
find ./ -name "*" -exec grep -n "string" ./ {} \;
 
使用 find 查找时希望忽略某个目录(-prune)：
如果希望在 /app 目录下查找文件， 但不希望在 /app/bin 目录下查找：
find /app -name "/app/bin" -prune -o -print
 
使用 type 选项：
如果要在 /etc 目录下查找所有的目录：
find /etc -type d -print
 
如果要在 /etc 目录下查找 .svn 的目录：
find /etc -name .svn -type d -print

为了在当前目录下查找除目录以外的所有类型的文件：
find . ! -type d -print
 
为了在当前目录下查找所有的符号链接文件， 可以用：
find . -type l -print
 
find .  -mtime -4（4天内）/4(4天前的那一天)/+4（5天前）

find         -name 
                -size   +50k
                -type    f/b/c/d/l/sp...
                -perm    -/+ /    755
                -uid/user      201/yangsf
                -gid/group    501/yangsf
                -nouser/nogroup   

===========================================================
为了用 ls -l 命令列出所匹配到的文件， 可以把 ls -l 命令放在find命令的 -exec 选项中：
find . -type f -exec ls -l {} \;
 
注： f 表示普通文件
    exec 选项后面跟随着所要执行的命令， 然后是一对 {}， 一个空格和一个 \，最后是一个分号。

```

