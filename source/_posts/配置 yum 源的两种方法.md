---
date: 2013-04-27T00:38:56

title: 配置 yum 源的两种方法

---

由于 redhat的yum在线更新是收费的，如果没有注册的话不能使用，如果要使用，需将redhat的yum卸载后，重启安装，再配置其他源，以下为详细过程： 


1.删除redhat原有的yum
```
rpm -aq|grep yum|xargs rpm -e --nodeps
```
2.下载yum安装文件
```
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-3.2.27-14.el6.centos.noarch.rpm 
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-metadata-parser-1.1.2-14.1.el6.x86_64.rpm 
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.26-11.el6.noarch.rpm 
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/python-iniparse-0.3.1-2.1.el6.noarch.rpm 
```
3.进行安装yum 
```
rpm -ivh python-iniparse-0.3.1-2.1.el6.noarch.rpm 
rpm -ivh yum-metadata-parser-1.1.2-14.1.el6.x86_64.rpm 
rpm -ivh yum-3.2.27-14.el6.centos.noarch.rpm um-plugin-fastestmirror-1.1.26-11.el6.noarch.rpm
```
注意最后两个包必需同时安装，否则会相互依赖 

4.更新repo文件
```
mv /etc/yum.repos.d/rhel-debuginfo.repo /etc/yum.repos.d/rhel-debuginfo.repo.repo.bak

vi /etc/yum.repos.d/rhel-debuginfo.repo
```
内容为：
```
[base]
name=CentOS-$releasever - Base
baseurl=http://mirrors.163.com/centos/6.0/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-6

#released updates
[updates]
name=CentOS-$releasever - Updates
baseurl=http://mirrors.163.com/centos/6.0/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-6

#packages used/produced in the build but not released
#[addons]
#name=CentOS-$releasever - Addons
#baseurl=http://mirrors.163.com/centos/$releasever/addons/$basearch/
#gpgcheck=1
#gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-6
#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
baseurl=http://mirrors.163.com/centos/6.0/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-6
#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus
baseurl=http://mirrors.163.com/centos/6.0/centosplus/$basearch/
gpgcheck=1
enabled=0
```
5、yum clean all 

6、yum install vim #测试一下可不可以用



【设置本地镜像作为yum源】
```
1、mkdir /mnt/cdrom

2   mount /dev/cdrom /mnt/cdrom  #挂着镜像，光盘

3  mkdir /home/redhat-iso

4  cp -Rf /mnt/cdrom/* /home/redhat-iso   #将光盘内的内容copy到某个路径下

5  vi /etc/yum.repos.d/rhel-debuginfo.repo  #编辑repo文件
```
添加一下内容：
```
[rhel_6_iso]
name=local iso 
baseurl=file:///home/redhat-iso/
gpgcheck=1
gpgkey=file:///home/redhat-iso/RPM-GPG-KEY-CentOS-6 #该处的gpgkey文件应该改成iso镜像中的文件名

```



【关于yum的一点资料】

一、yum是什么
yum = Yellow dog Updater, Modified
主要功能是更方便的添加/删除/更新RPM包.
它能自动解决包的倚赖性问题.
它能便于管理大量系统的更新问题


二、 yum特点
*可以同时配置多个资源库(Repository)
*简洁的配置文件(/etc/yum.conf，/etc/yum.repos.d 下的文件)
*自动解决增加或删除rpm包时遇到的倚赖性问题
*使用方便
*保持与RPM数据库的一致性

三、 yum安装
Centos/RHEL
1. #rpm -ivh yum-2.4.3-4.el4.centos.noarch.rpm

四、yum配置
yum的配置文件
yum的一切配置信息都储存在一个叫yum.conf的配置文件中，通常位于/etc目 录下，这是整个yum系统的重中之重，所以有必要详细介绍。下面是一个从网上找来的yum.con文件，让我们以此为例，进行说明。

[main] 
cachedir=/var/cache/yum 
debuglevel=2 
logfile=/var/log/yum.log 
pkgpolicy=newest 
distroverpkg=redhat-release 
tolerant=1 
exactarch=1 
retries=1 

[base] 
name=Fedora Core $releasever - $basearch - Base 
baseurl=http://download.atrpms.net/mirrors/fedoracore/$releasever/$basearch/os 
http://rpmfind.net/linux/fedora/cor...er/$basearch/os 
http://mirror.clarkson.edu/pub/dist...er/$basearch/os 

[updates-released] 
name=Fedora Core $releasever - $basearch - Released Updates 
baseurl=http://download.atrpms.net/mirrors/fedoracore/updates/$releasever/$basearch 
http://redhat.linux.ee/pub/fedora/l...sever/$basearch 
http://fr2.rpmfind.net/linux/fedora...sever/$basearch 

[Fedora.us] 
name=Fedora.us - $basearch - Extras 
baseurl=http://fedora.linux.duke.edu/fedorax86_64/fedora.us/$releasever/$basearch/RPMS.stable 

[Dag Wieers] 
name=Dag RPM Repository for Fedora Core 
baseurl=http://apt.sw.be/fedora/$releasever/en/$basearch/dag 

[Livna] 
Name=Livna RPM, Fedora Core $releasever - $basearch 
baseurl=http://rpm.livna.org/fedora/$releasever/$basearch/RPMS.stable 

[freshrpms] 
name=FreshRPMs 
baseurl=http://ayo.freshrpms.net/fedora/linux/$releasever/$basearch/freshrpms/ 
http://ftp.us2.freshrpms.net/linux/...arch/freshrpms/
 

第一部分(这是yum的全局性设置。默认一般不必改动。)

[main]
cachedir：yum缓存的目录，yum在此存储下载的rpm包和数据库，一般是/var/cache/yum。
debuglevel：除错级别，0-10,默认是2。
logfile：yum的日志文件，默认是/var/log/yum.log。
pkgpolicy：包的策略。一共有两个选项，newest和last，这个作用是如果你设置了多个repository，而同一软件在不同的repository中同时存在，yum应该安装哪一个，如果是newest，则yum会安装最新的那个版本。如果是last，则yum会将服务器id以字母表排序，并选择最后的那个服务器上的软件安装。一般都是选newest。
distroverpkg：指定一个软件包，yum会根据这个包判断你的发行版本，默认是redhat-release，也可以是安装的任何针对自己发行版的rpm包。
exactarch，有两个选项1和0,代表是否只升级和你安装软件包cpu体系一致的包，如果设为1，则如你安装了一个i386的rpm，则yum不会用1686的包来升级。
retries，网络连接发生错误后的重试次数，如果设为0，则会无限重试。
tolerent，也有1和0两个选项，表示yum是否容忍命令行发生与软件包有关的错误，比如你要安装1,2,3三个包，而其中3此前已经安装了，如果你设为1,则yum不会出现错误信息。默认是0。

除了上述之外，还有一些可以添加的选项，如

exclude=，排除某些软件在升级名单之外，可以用通配符，列表中各个项目要用空格隔开，这个对于安装了诸如美化包，中文补丁的朋友特别有用。
gpgchkeck= 有1和0两个选择，分别代表是否是否进行gpg校验，如果没有这一项，默认好像也是检查的。

第二部分：

配置repository服务器了，这是最令人激动的，有了好的repository，就如家门口开了大卖场，要什么东西稍微跑跑腿就行，对了这还是个免费的大卖场。
所有服务器设置都应该遵循如下格式：

1. [serverid]
2. name=Some name for this server
3. baseurl=url://path/to/repository/

其中serverid是用于区别各个不同的repository，必须有一个独一无二的名称。
name，是对repository的描述，支持像$releasever $basearch这样的变量;
baseurl是服务器设置中最重要的部分，只有设置正确，才能从上面获取软件。它的格式是：

1. baseurl=url://server1/path/to/repository/
2. url://server2/path/to/repository/
3. url://server3/path/to/repository/

其中url支持的协议有 http:// ftp:// file://三种。baseurl后可以跟多个url，你可以自己改为速度比较快的镜像站，但baseurl只能有一个，也就是说不能像如下格式：

1. baseurl=url://server1/path/to/repository/
2. baseurl=url://server2/path/to/repository/
3. baseurl=url://server3/path/to/repository/

其中url指向的目录必须是这个repository header目录的上一级，它也支持$releasever $basearch这样的变量。
url之后可以加上多个选项，如gpgcheck、exclude、failovermethod等，比如：

1. [updates-released]
2. name=Fedora Core $releasever - $basearch - Released Updates
3. baseurl=http://download.atrpms.net/mirrors/fedoracore/updates/$releasever/$basearch
4. http://redhat.linux.ee/pub/fedora/linux/core/updates/$releasever/$basearch
5. http://fr2.rpmfind.net/linux/fedora/core/updates/$releasever/$basearch
6. gpgcheck=1
7. exclude=gaim
8. failovermethod=priority

其中gpgcheck，exclude的含义和[main]部分相同，但只对此服务器起作用，failovermethode 有两个选项roundrobin和priority，意思分别是有多个url可供选择时，yum选择的次序，roundrobin是随机选择，如果连接失 败则使用下一个，依次循环，priority则根据url的次序从第一个开始。如果不指明，默认是roundrobin。

几个变量

$releasever，发行版的版本，从[main]部分的distroverpkg获取，如果没有，则根据redhat-release包进行判断。
$arch，cpu体系，如i686,athlon等
$basearch，cpu的基本体系组，如i686和athlon同属i386，alpha和alphaev6同属alpha。

对yum.conf设定完成，我们就可以好好体验yum带来的方便了。

五、RHEL/CENTOS的yum源
1、修改配置文件
2、导入GPG KEY

导入每个reposity的GPG key，前面说过，yum可以使用gpg对包进行校验，确保下载包的完整性，所以我们先要到各个repository站点找到gpg key，一般都会放在首页的醒目位置，一些名字诸如 RPM-GPG-KEY.txt之类的纯文本文件，把它们下载，然后用rpm --import xxx.txt命令将它们导入，最好把发行版自带GPG-KEY也导入。rpm --import /usr/share/doc/redhat-release-*/RPM-GPG-KEY 官方软件升级用的上。

六、扩展你的rpm包
1、rpmforge
Centos/RHEL默认的yum软件仓库非常有限,仅仅限于发行版本那几张盘里面的常规包和一些软件包的更新，利用RpmForge，可以增加非常多的第三方rpm软件包。

获取
http://dag.wieers.com/rpm/packages/rpmforge-release/
找到与系统平台架构匹配的rpmforge包安装之即可。安装完后.默认会在系统的/etc/yum.repos.d/下新增两个文件,mirrors-rpmforge和rpmforge.repo

安装
# rpm -ivh rpmforge-release-0.3.6-1.el4.rf.i386.rpm
# rpm --import http://dag.wieers.com/rpm/packages/RPM-GPG-KEY.dag.txt

2、加速yum更新速度
安装yum的fast mirror插件，可以加快 CentOS/RHEL yum的速度和提高稳定性，效果显著。
centos5 
 #yum -y install yum-fastestmirror
centos4
 yum -y install yum-plugin-fastestmirror

 

七、 yum使用
注:当第一次使用yum或yum资源库有更新时,yum会自动下载所有所需的headers放置于/var/cache/yum目录下,所需时间可能较长.

系统更新(更新所有可以升级的rpm包,包括kernel)
#yum -y update

 

每天定期执行系统更新
#chkconfig yum on
#service yum start

 

*rpm包的更新，检查可更新的rpm包
#yum check-update

 

更新所有的rpm包
#yum update

 

更新指定的rpm包,如更新kernel和kernel source
#yum update kernel kernel-source

 

大规模的版本升级,与yum update不同的是,连旧的淘汰的包也升级
#yum upgrade

 

*rpm包的安装和删除
安装rpm包,如xmms-mp3
#yum install xmms-mp3

 

删除rpm包,包括与该包有倚赖性的包
#yum remove licq
注:同时会提示删除licq-gnome,licq-qt,licq-text

 

*yum暂存(/var/cache/yum/)的相关参数
清除暂存中rpm包文件
#yum clean packages

 

清除暂存中rpm头文件
#yum clean headers

 

清除暂存中旧的rpm头文件
#yum clean oldheaders

 

清除暂存中旧的rpm头文件和包文件
#yum clean 或#yum clean all
注:相当于yum clean packages + yum clean oldheaders

 

*rpm包列表
列出资源库中所有可以安装或更新的rpm包
#yum list

 

列出资源库中特定的可以安装或更新以及已经安装的rpm包
#yum list mozilla
#yum list mozilla*
注:可以在rpm包名中使用匹配符,如列出所有以mozilla开头的rpm包

 

列出资源库中所有可以更新的rpm包
#yum list updates

 

列出已经安装的所有的rpm包
#yum list installed

 

列出已经安装的但是不包含在资源库中的rpm包
#yum list extras
注:通过其它网站下载安装的rpm包
*rpm包信息显示(info参数同list)

 

列出资源库中所有可以安装或更新的rpm包的信息
#yum info

 

列出资源库中特定的可以安装或更新以及已经安装的rpm包的信息
#yum info mozilla
#yum info mozilla*
注:可以在rpm包名中使用匹配符,如列出所有以mozilla开头的rpm包的信息

 

列出资源库中所有可以更新的rpm包的信息
#yum info updates

 

列出已经安装的所有的rpm包的信息
#yum info installed

 

列出已经安装的但是不包含在资源库中的rpm包的信息
#yum info extras
注:通过其它网站下载安装的rpm包的信息

 

*搜索rpm包
搜索匹配特定字符的rpm包
#yum search mozilla
注:在rpm包名,包描述等中搜索

 

搜索有包含特定文件名的rpm包
#yum provides realplay

 

八、yum常用问题解决
1、如果网速慢的话可以通过增加yum的超时时间，这样就不会总是因为超时而退出。
#vi /etc/yum.conf
#加上这么一句
timeout=120

 

2、yum Existing lock错误的解决办法
如果系统启动的时候， yum出现Existing lock /var/run/yum.pid: another copy is running as pid 3380. Aborting.可以用下面的办法解决：
方法一
etc/init.d/yum-updatesd stop

方法二
#rm -f /var/run/yum.pid
主要原因就是yum在自动更新，只要关掉它就可以了。

 

先安装fastestmirror，让系统yum的时候自动选择最快的镜像：

#yum install yum-fastestmirror -y

接下来说说RPMForge。

RPMForge是一个安全、稳定并且更新及时的软件仓库，通过安装RPMForge可以是CentOS在进行yum update的时候多一个选择，有时候甚至是唯一的选择，比如想要升级rsync到3.0.x版本，如果懒得自己编译安装，就得依靠RPMForge了。

1、先安装priority这个yum的插件，这个插件用来设定安装软件时候软件仓库的优先级，一般是默认先从官方base或者镜像安装，然后从社区用户contribute的软件中安装，再从第三方软件仓库中安装。
#yum install yum-priorities

2、手动编辑 /etc/yum.repos.d/ 目录中后缀为.repos的文件来设置软件仓库的优先级
priority=N(N是整数，范围从1-99，数字越小代表优先级越高)

官方推荐配置是
[base], [addons], [updates], [extras] 这几部分 priority=1
[centosplus],[contrib] 这几部分priority=2
Third Party Repos such as rpmforge … priority=N  (where N is > 10 and based on your preference)

3、现在开始安装rpmforge的软件仓库
先下载rpmforge的安装包
      i386  wget http://apt.sw.be/redhat/el5/en/i386/RPMS.dag/rpmforge-release-0.3.6-1.el5.rf.i386.rpm

      x86_64 wget http://apt.sw.be/redhat/el5/en/x86_64/RPMS.dag/rpmforge-release-0.3.6-1.el5.rf.x86_64.rpm

安装

     rpm -i rpmforge-release-0.3.6-1.el5.rf.*.rpm

更改 /etc/yum.repos.d/rpmforge.repo 配置文件，添加
priority=1（或者2.3.4….）

现在就可以使用rpmforge这个软件仓库了。比如yum update rsync，嘿嘿，成功升级到3.0.5了。

来源： http://www.cnblogs.com/shuaixf/archive/2011/11/30/2268496.html
