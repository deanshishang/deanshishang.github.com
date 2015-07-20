---
layout: post
title: openwrt系统gpio支持
category: 技术
tags: Openwrt
keywords:
description:
---

在嵌入式设备中对GPIO的操作是最基本的操作，General Purpose Input Output -> 总线扩展器，利用工业标准I2C SMBus或SPI接口简化了I/O的扩展，当微控制芯片组没有足够的I/O端口，或者当系统需要采用远端串行通信或者控制时，GPIO能够提供额外的控制和监视功能；

##### 给opewnrt添加LED驱动的例子

当给新的路由器刷好OPENWRT之后，嵌入式板子的LED灯都乱了或者不亮了，这个时候考虑到LED都是通过GPIO连接的，所以在初始化LED之前，必须初始化GPIO。

驱动GPIO不用自己写，linux的platform Init部分已经写好了初始化代码了；

Linux下实现LED驱动非常方便，基本上要做的事情就是定义一下LED所在的GPIO的针脚；

具体参考： http://www.wifizoo.net/viewthread.php?tid=41841

##### /sys/class/gpio 文件接口操作IO端口

可以单独写一个驱动程序表示gpio的操作，其实Linux下边有一个通用的GPIO操作接口那就是"/sys/class/gpio"方式

具体参考：http://blog.csdn.net/mirkerson/article/details/8464231
