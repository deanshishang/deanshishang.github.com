---
layout: post
title: Linux系统脚本启动顺序分析，rc.local, rc.d, rc.sysinit等
category: Linux
---

### Redhat

加载内核
执行init程序
init执行第一个脚本
/etc/rc.d/rc${RUNLEVEL}d/* 缺省的运行模式
/etc/rc.local 相应级别的服务启动之后，再执行该文件

init执行的第一个脚本与运行模式无关的相同初始化任务：

* 调入keymap以及系统字体
* 启动swapping
* 设置主机名
* 设置NIS域名 提供使用者账号密码家目录档名等
* 检查(fsck) 并Mount文件系统
* 打开quota
* 装在声卡模式
* 设置系统时钟
* 其他

执行完这个脚本之后系统根据运行模式选择相应的/etc/rc.d/rcX.d，其中运行模式从/etc/inittab获取，执行完这些之后就直接执行/etc/rc.local内容。

接下来等待登录，登录时会执行一些用户环境的初始化脚本(bashrc,bash_profile等shell配置文件)

#### 运行级别runlevel

##### INIT

init是linux系统中不可或缺的程序之一，由内核启动的用户级进程，内核自行启动(已经被载入内存，开始运行，并且初始化所有的设备驱动程序和数据结构等)之后，通过启动一个用户级程序Init的方式，完成引导进程，所以，init始终是第一个进程(进程编号始终为1),内核会在过去曾经使用过Init的几个地方查找它，正确位置是/sbin/init，如果系统找不到init, 试着运行/bin/sh，如果失败，系统启动也会失败。

##### 运行级别

定义

* 0 - 停机
* 1 - 单用户模式
* 2 - 多用户没有NFS(网络文件系统)
* 3 - 完全多用户模式，命令行文字界面
* 4 - 系统保留
* 5 - X11 多用户图形模式，采用xwindow
* 6 - 重新启动

这些级别在/etc/inittab中指定，是init进程主要寻找的文件，在大多数linux发型版本中，启动脚本都位于/etc/rc.d/init.d/中，用ln 连接到/etc/rc.d/rcn.d目录。

查看运行级别

runlevel
N 2 

N代表之前没有用别的级别


init命令控制运行级别
---重启
init 6
---关机
init 0

修改默认运行级别，前面已经讲过，修改inittab文件
id:3:initdefault:

这些命令平常使用很少，不能乱用。

#### /etc/rcX.d /etc/rc.d/rcX.d

/etc/rc.d/目录下文件 /etc/init.d 也是用类似的软连接的方式

/etc/rcX.d/下的文件多事以S或K开始数字中间再接上服务名称，S表示启动，K表示杀死，数字表示启动顺序，来自于/etc/rc.d/init.d脚本内部，表示顺序，从小到大依次执行。

### Ubuntu的不同

与传统的不同，使用upstart完成系统的启动，但是表面上仍然维持init的形式，默认Init读取/etc/inittab文件中的缺省级别设置不一样，默认是找不到/etc/inittab文件的，运行级别也有差别，默认情况下级别2 3 4 5 都是一样的，即完整的多用户模式，默认的设定写在/etc/init/rc-sysinit.conf文件中，可看到这样一句：

env DEFAULT_RUNLEVEL=2

表明系统进入级别是2

精确确认级别之后，init进一步运行/etc/init.d/rc，然后根据级别进入/etc/rcX.d启动或者关闭相关任务；

服务的启动与关闭脚本
rc-sysinit.conf或inittab中指定的默认级别是2，那么init执行/etc/rc2.d目录中的脚本启动或者关闭服务，目录下文件都是软连接到/etc/init.d下边，根据不同级别的需求，在对应rcX.d下放一个不同的服务，S开头的会传递一个start参数，K开头传递一个stop参数，由此关闭服务，另外还有一个rcS.d目录，这个目录下的服务脚本与rcX.d类似，但是会在rc0-6.d中的脚本执行之前首先被执行。

服务的安装
根据理解，将一个软件安装为服务也就比较清楚了，就是在/etc/init.d中添加一个服务的启动脚本，然后在需要启动服务对应的级别的/etc/rcX.d中按照文件名格式添加一个指向/etc/init.d中脚本的符号链接。

apache2为例，默认情况下，apache2在/usr/local/apache2，启动脚本是/usr/local/apache2/bin/apachectl,操作是：

sudo cp /usr/local/apache2/bin/apachectl /etc/init.d/httpd

手动添加任务 在/etc/rc2.d中 

sudo ln -s /etc/init/d/httpd /etc/rc2.d/S80httpd

然后用 sysv-rc-conf或者chkconfig -list 检查一下就能看到已经运行在级别2下简历名为httpd服务，重启后，系统会自动启动apache2。

现在手动启动关闭重启httpd服务器可以用service+httpd+参数形式；

除了手动作链接以外，还可用一些工具软件实现，比如常用的update-rc.d chkconfig sysv-rc-conf等
update-rc.d为例：

sudo update-rc.d httpd start 80 2 3 4 5 .

最后的.不能少，如果要删除httpd服务，用以下命令就删掉/etc/rcX.d中所有指向/etc/init.d/httpd的链接

sudo update-rc.d httpd remove

chkconfig 是有图形界面的 操作就比较容易了。

##### rc.local

在/etc/rcX.d目录下都会有一个S99rc.local，表示最后都会调用这个rc.local脚本，而/etc/inid.d/rc.local会检查是否存在/etc/rc.local，并运行之，因此就可以在rc.local中写入代码，随系统启动某些程序，实现类似的服务功能。

综上所诉：系统的启动调用过程如下：

###### 内核->/etc/init/rc-sysinit.conf->[/etc/inittab]-> /etc/init.d/rc -> /etc/rcX.d -> /etc/init.d/rc.local -> /etc/rc.local

### 其他LINUX系统

其他的文件结构和过程略有不同，整合一下Redhat的centos系统，系统中默认的Init使用的是/etc/inittab文件，读取/etc/rc.sysinit,再根据级别进入/etc/rcX.d，其中/etc/rc.sysinit指向/etc/rc.d/rc.sysinit的链接，/etc/rcX.d是指向/etc/rc.d/rcX.d的链接，/etc/rc.local是指向/etc/rc.d/rc.local的连接，所以启动顺序如下：

###### 内核 —> /etc/inittab -> /etc/rc.sysinit（/etc/rc.d/rc.sysinit）-> /etc/rcX.d（/etc/rc.d/rcX.d）-> /etc/rc.local(/etc/rc.d/rc.local)
