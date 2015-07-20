---
layout: post
title: 内核kernel以及文件系统rootfs如何映射到nandflash地址的，分区信息
category: 技术
tags: Linux
keywords:
description:
---

先来看一个nandflash分区的例子：
Flash作为MTD设备使用；

static struct mtd_partition smdk_default_nand_part[]={
[0]={
name:"bootloader", //分区1，引导程序
size:0x00100000, //大小1M
offset:0x0, //起始地址(偏移量：0)
},	
[1]={
name:"kernel", //分区2，内核
size:0x00300000, //大小3M
offset:0x00100000, //偏移量1M，正好接在上一个分区之后
},	
[2]={
name:"rootfs", //分区3,文件系统
size:0x02800000, //大小3M
offset:0x00400000, //偏移量4M，0x00100000+0x00300000
},	
[3]={
name:"art",
size:0x00f00000,//15M
offset:0x02d00000, //偏移量 0x00100000+0x00300000+0x02800000
}
};

这是规划的一个60M的nandflash，Bootloader留出来1M，足够用了，内核空间要烧写到0x00100000的位置上，最大占用空间是3M，也足够了，做好U-boot后可以用jtag烧写到目标板，Nandflash的0x0处，然后启动u-boot：

设置好参数：
setenv ipaddr 192.168.1.2
setenv serverip 192.168.1.10
saveenv
tftp 0x3008000 zImage
go 0x30008000

解压完内核到了 Done.Boot the kernel. 就没有输出了这是没有向内核传参造成的。

setenv bootargs 'console=ttySAC0, 115200'
saveenv
这样就好了。如果打印出来的分区信息和自定义的一样，那就正确了。

例子只是简述了nandflash的分区信息设计一个启动内核的过程简述，下面主要分析内核以及文件系统如何映射到对应的nandflash地址的？

问题引入：内核在启动的过程中是如何完成将本地的flash设备映射成文件系统的，比如内核文件ramdisk.image.gz，烧写在了flash的0x10140000处，内核在启动的过程中如何将这个文件映射成根目录和资子目录的，ramdisk.image.gz在flash中的位置如果发生了变化，如何修改内核？

首先，image.gz是内核的话，就不是将这个文件映射成根目录和子目录的，而是对应的根文件系统，简称rootfs对应着根目录及各子目录和文件，那么过程来了：

##### 系统启动过程简介

初始化代码读取uboot到内存里边，加载一些驱动，其中包括nandflash的驱动，然后根据Uboot里边设置的一个启动命令：

nand read 0x30007fc0 0x100000 0x200000; bootm 0x30007fc0

先去读取nandflash从0x100000开始长度为0x200000的数据到memory的0x30007fc0处，然后bootm表示从memory的0x30007fc0开始运行，也就是运行你的内核镜像了，此处也就是你的ramdisk.image.gz，而你的地址是0x10140000所以上边的启动命令至少0x100000要改成你的地址0x10140000，然后内核会自行解压缩，然后执行，初始化硬件模块，加载驱动模块，最后挂载rootfs，此文件是什么格式的是从uboot里边定义的：

#define CONFIG_BOOTARGS "root=/dev/mtdblock2 rw init=/linuxrc console=ttyS0,115200 mem=16M rootfstype=yafffs2"

并且从uboot转向内核运行的时候，传递给内核的，这样内核在加载rootfs的时候，才知道，要以什么格式，比如上面的yaffs2格式，
去加载此文件系统。
此文件系统，也是你实现自己用相应的文件系统制作工具，制作的，然后烧写到对应的位置的。
上面中root=/dev/mtdblock2，表示，要去/dev/mtdblock2,也就是你的mtd的第3个分区，去加载。
而这里的mtd的第3个分区具体对应的nand flash中的的地址，
是你在内核中，一般是在core.c自己定义的的nand flash的分区。
一般是uboot是第一个分区，内核kernel是第二个，然后就是rootfs是第三个分区，也就是/dev/mtdblock2。
随便网上给你找个别人的分区：

static struct mtd_partition rm9200_partitions[3]=
{
	{ //uboot 256K
		.name = "uboot",
		.size = 0x40000,
		.offset = 0
	},
	{ //kernel 1.768M
		.name = "kernel",
		.size = 0x1c0000,
		.offset = 0x40000
	},
	{ //rootfs 2M
		.name = "rootfs",
		.size = 0x200000,
		.offset = 0x200000
	},
}

按照上边的分区，定义的/dev/mtdblock2的起始地址就是0x200000,换算成大小是2M的位置，然后内核启动挂载rootfs的时候，就是上边的uboot传过来的yafffs2格式，到nandflash的2M的地址读取并加载rootfs，完成之后，根目录以及所有文件，文件夹就可以识别了，然后才会去读取并运行初始化脚本相关的东西，最后初始化console控制台，与系统交互。所以你说的位置如何映射成/根目录的就是这个rootfs对应着mtdblock2，对应的某个nandflash上的地址，比如是2M的位置，而不是内核kernel文件映射的，内核对应的是mtdblock1。

如果ramdisk.image.gz在flash中发生了变化，应该如何修改内核啊？
如果地址变化了，那么uboot中定义的启动参数：

nand read -x3007fc0 0x100000 0x200000;bootm 0x30007fc0 中的0x100000换成你的新地址就可以了。



#### 其他知识

我们烧写程序的时候，实际是先将程序烧写到了内存中，然后由内存搬运到NANDflash中，memcpy操作实现的。
