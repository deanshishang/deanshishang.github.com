---
layout: post
title: 基于openwrt操作系统，mips体系结构的路由器设备学习二
category: openwrt
---

##### 系统架构

完成了刷机工作，这个时候系统进入了系统，并且格式化了它的"可写"分区。不同处理器的openwrt系统的分区是有区别的，不是所有的分区都完全相同，在路由器上的flash中，内核中所使用的是驱动是ＭＴＤ设备驱动；

ＭＴＤ(Ｍemory Technology Device,内存技术设备)是用于访问内存类设备(ROM FLASH)LINUX 驱动子系统，主要目的是使ＦＬＡＳＨ类设备访问更加容易，为此在硬件和上层提供一个一个抽象层的接口，在操作系统下可以像访问硬盘一样操作这个设备。

在openwrt系统中现在对atheros方案实现了自动查找分区结尾；

几个分区中rootfs是完整的系统文件包含只读和可写，rootfs_data是在rootfs中的可写部分的位置，art分区保存了无线的硬件参数；

		uboot

		kernel

		rootfs  --->包含只读的和可写的，只读的文件系统挂载为/rom

		rootfs_data  --->可写的部分为此，挂载为/overlay，这是一个分区逻辑。

		art

想看某个分区的内容的话执行 dd if=/dev/mtdblockX of=/tmp/X.bin，然后执行

hexdupm -C /tmp/X.bin 就可以查看分区内容了。



