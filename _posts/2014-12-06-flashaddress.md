---
layout: post
title: 路由器编程器固件uboot,fw,和art在flash中的位置例子
category: openwrt
---

##### 编程器固件包括u-boot,fw,art。

u-boot像电脑的bios，是底层的管理系统；

fw像电脑的操作系统，实现路由器的各种功能；

art，像电脑的无线驱动程序，是无线校验码；

##### 大部分的固件中：

u-boot的大小是128KB(0x20000);

art的大小是64KB(0x10000);

fw的大小有4M与8M区别，4M的为3840KB(0x3c0000)????,8M的为7936KB(0x7c0000)，刷机之前要对刷入flash的uboot,fw,art的文件长度用ultaedit或者winhex校验，尤其是uboot如果大小不对，不要尝试刷入否则变砖；

##### uboot fw art 在flash中的位置如下：

4M flash的：

flash地址起始：0x000000--0x3fffff

ttl访问flash的地址：0x9f000000--0x9f3fffff

        flash起始地址   TTL起始地址  flash终止地址  TLL终止地址

uboot   0x000000        0x9f000000   0x01ffff       0x9f01FFFF

fw      0x020000        0x9f020000   0x3dffff       0x9f3dffff

art     0x3f0000        0x9f3f0000   0x3fffff       0x9f3fffff

8M flash的：

flash起始地址：0x000000--0x7fffff

ttl访问的flash地址：0x9f000000--0x9f7fffff

        flash起始地址   TTL起始地址  flash终止地址  TLL终止地址

uboot   0x000000        0x9f000000   0x01ffff       0x9f01FFFF

fw      0x020000        0x9f020000   0x7dffff       0x9f7dffff

art     0x7f0000        0x9f7f0000   0x7fffff       0x9f7fffff

##### 刷固件tftp方式

4M的：

编程器固件

tftp 0x80000000 full.bin

erase 0x9f000000 +0x400000

cp.b 0x80000000 0x9f000000 0x400000

Uboot

tftp 0x80000000 uboot.bin

erase 0x9f000000 +0x20000

cp.b 0x80000000 0x9f000000 0x20000

fw

tftp 0x80000000 fw.bin

erase 0x9f020000 +0x3c0000

cp.b 0x80000000 0x9f020000 0x3c0000

art

tftp 0x80000000 art.bin

erase 0x9f3f0000 +0x10000

cp.b 0x80000000 0x9f3f0000 0x10000

8M的改变fw大小即可。


