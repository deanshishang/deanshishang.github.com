---
layout: post
title: Tig - bootm,bootargs,bootcmd等Uboot环境变量
category: 技术
tags: Linux
keywords:
description:
---

Uboot的环境变量：

bootcmd: 是Uboot启动的时候默认执行的一些命令，可以配置不同的参数；

bootm 是专门用于启动在sdram中的用u-boot的mkimage工具处理过的内核映像，而且这个映像在执行bootm的时候要保证Image文件已经在内存中了；

bootargs: 内核与文件系统的不同搭配就会有不同的设置方法，甚至也可以不设置bootargs，而是直接将其写到内核中去(配置内核的选项中可以进行这样的设置)；
##### bootargs常用参数介绍：

* root 用来指定rootfs的位置，常见的

  root=/dev/mtdx rw
  root=/dev/mtdblockx rw
  root=/dev/mtdblock/x rw
  root=31:0x

  上边的情况是通用的，mtd是字符设备，mtdblock是块设备，如果不知道可以挨个试，root=/dev/nfs
  在文件系统为基于nfs的文件系统的时候使用。当然指定root=/dev/nfs之后，还需要指定nfsroot=serverip:nfs_dir，即指明文件系统存在那个主机的那个目录下面；

* rootfstype 跟root配合使用，一般如果根文件是ext2的话可以省略，但是如果是Jffs squashfs等的话，就需要指定了，不然无法挂载根分区。

* console console=tty使用虚拟串口终端设备，console=ttyS[,options]使用特定的串口，options可以是bbbbpnx，bbbb是串口波特率，P是奇偶位，n是指的bits console=ttySAC[,options]同上边；

* mem mem=xxM指定内存大小，不是必须的；

* ramdisk_size ，告诉ramdisk驱动，创建的ramdisk的size，默认的是4M；

* initrd ,noinitrd , 但你没有使用ramdisk启动系统的时候，需要使用Noinitrd这个参数，如果使用了的话，就需要指定Initd=r_addr,size r_addr表示Initrd在内存中的位置，size表示initrd的大小；

* init , 指定内核启动起来之后，进入系统运行的第一个脚本，一般是init=/linuxrc init=/etc/preinit, preinit的内容一般是创建consolenull设备节点，运行init程序，挂载一些文件系统等等操作，/linuxrc一般是一个连接罢了；

* mtdparts , mtdparts=fc000000.nor_flash:1920k(linux),128k(fdt),20M(ramdisk),4M(jffs2),38272k(user),256k(env),384k(uboot)
  要想用这个参数，内核中的mtd驱动必须要支持，即内核配置时需要选上Device Drivers-->Memory Technology Device support--> Command line partition table parsing
  mtdparts的格式是这样的：
  mtdparts=[;
  :=[@offset][][ro]
  :=unique id used in mapping driver/device
  :=standard linux memsize Or "-" to denote all remaining space
  :=(NAME)
  因此使用的时候按照下边格式：
  mtdparts=mtd-id:@(),@()
  这里几个需要注意的是：
  mtd-id必须要跟你当前平台的flash的mtd-id一致的，不然整个mtdparts会失效，size在设置的时候可以为实际的size(xxM,xxk,xx),也可以用'-'表示剩余的所有空间。
  举例：
  假设flash的mtd-id是sa100，那么可以用下面的方式来设置：
  mtdparts=sa1100:- 表示只有一个分区
  mtdparts=sa1100:256k(ARMboot)ro,-(root)有两个分区
  可以查看drivers/mtd/cmdlinpart.c

* ip ,指定系统启动之后网卡的Ip地址，如果你使用基于nfs的文件系统，必须需要这个参数，其他可以看自己喜好：两种设置方法：
  ip = ip addr
  ip = ip addr:server ip addr:gateway:netmask::which netcard:off netcar指开发板上的网卡，不是主机的网卡；

#### 常见的集中Bootargs

* 文件系统是ramdisk，直接在内存中，设置如下

  setenv bootargs 'initrd=0x32000000,0xa00000 root=/dev/ram0 console=ttySAC0 mem=64M init=/linuxrc'

* 文件系统是ramdisk，在flash中，Bootargs设置如下：
 
  setenv bootargs 'mem=32M console=ttyS0,115200 root=/dev/ram rw init=/linuxrc'

* 文件系统是jffs2, 在flash中，设置如下：

  setenv bootargs 'mem=32M console=ttyS0,115200 noinitrd root=/dev/mtdblock2 rw rootfstype=jffs2 init=/linuxrc'

* 如果是nfs的，则设置：
  
  setenv bootargs ‘noinitrd mem=64M console=ttySAC0 root=/dev/nfs nfsroot=192.168.0.3:/nfs ip=192.168.0.5:192.168.0.3:192.168.0.3:255.255.255.0::eth0:off’
  或者
  setenv bootargs ‘noinitrd mem=64M console=ttySAC0 root=/dev/nfs nfsroot=192.168.0.3:/nfs ip=192.168.0.5’

