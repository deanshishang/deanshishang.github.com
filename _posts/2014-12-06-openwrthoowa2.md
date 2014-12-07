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


##### 系统的文件结构

uboot启动了kernel完成之后，由Kernel加载ROM分区(就是rootfs减去rootfs_data那一部分)；

ROM分区采用的是linux内核支持的squashFS文件系统(一种压缩只读文件)，加载完毕以后挂载到/rom目录，同时也挂载为根文件系统；

系统使用JFFS2文件系统格式化rootfs_data这部分并且将这部分挂载到/overlay目录；

将/overlay挂载为/分区；

将一部分内存挂载为/tmp目录；

到底根文件系统是哪个？Openwrt设计的时候，是overlay透明挂载，首先将/rom挂载为根文件系统，然后再用/overlay覆盖在/之上，这样当文件系统变更修改的时候就是修改/overly中，rom是不改变的；

##### 网络配置

板子中一般是WAN口是eth1,LAN口是eth0，在配置文件/etc/config/network中，一般loopback代表本机自环配置，lan表示LAN口配置，wan表示WAN口配置，switch表示VLAN配置(虚拟局域网)，switch_vlan表示VLAN1的参数；

ifconfig可以查看网络设备，一般有以下类型：

br-lan:虚拟设备，用于lan口设备桥接的，目前路由器普遍将有线路LAN口和WIFI无线接口桥接在一起作为统一的LAN；

lo：虚拟设备，自环设备；

eth0 eth1：可能会划分为VLAN或者WAN口；

wlan0: 实设备，启动了wifi功能以后产生此设备；

pppoe-wan：虚拟设备，在pppoe拨号的时候产生。

命令：

查看br-lan桥接设备： brctl show

查看vlan配置：这里可以看到芯片中支持的网口配置情况，WAN口不在这里，Port 0是到cpu的接口： swconfig dev eth0 show

查看系统的状态日志，执行命令： logread

查看无线状态：opkg install iwinfo     iwinfo

##### 配置WAN口参数

WAN口是用于链接外网的端口，可以被用于配置成多种方式链接外网；

配置文件结构：

config interface "wan"
option ifname "eth1"
option proto "协议类型"
...

协议类型可选参数：

static:静态ip地址；

dhcp:通过外网dhcp服务器获得ip地址；

pppoe:通过pppoe拨号获得ip地址；

pptp:链接远程vpn服务器；

3g:链接3/4G无线电话网络上网；

具体其他配置待续....




