---
layout: post
title: openwrt使用的文件系统jffs2与squashfs, lzma， 文件系统加载分析
category: 技术
tags: Openwrt
keywords:
description:
---

open多数选择硬件flash，一般使用jffs2和squash文件系统，前者是可写的，后者是只读的，一般会把jffs2mount到某个目录下，这样就存在目录如/bin就是只读的，某些其他目录是可读写的，这样对文件的操作依赖于文件系统的属性以及路径。

openwrt使用了mini_fo文件系统，从用户的角度看，会觉得整个文件系统都是可写的，用户可以任意增加删减修改，这种文件系统可以认为是squash fs和jffs2的文件系统上实现了一个符号连接，如果用户读取只读文件，则链接到squash文件系统，如果对只读文件进行修改，将修改的文件放到Jffs2文件系统上，并修改链接。如果用户不采用jffs2系统，会使用ramfs这样也可是实现目的，但是重启后就丢失了；

Openwrt启动之后，Linux内核加载squash文件系统，是只读的；
openwrt在初始化时，会以mini_fo的文件系统类型重新Mount整个文件系统，整个过程在/etc/preinit/的脚本中实现，有一行代码实现：

mount -t mini_fo -o base=/,sto=$1 "mini_fo:$1" /mnt 2>&- && root=/mnt


jffs2文件系统格式适合于断电系统，不像FAT那样容易丢失文件，因为路由器都容易突然断电；

官方的jffs2格式刷到路由器就是一个jffs2分区，ROM本身和以后安装的软件都在这个区里可以读写；

squashfs是把rom压缩到一个文件刷金路由器，然后剩下的空间格式化成jffs2并且优于ROM文件，rom的只读分区挂载到/rom下，另一个读写jffs2分区挂到/overlay,当两个目录下有同名文件就优先读这个，相当于覆盖了rom文件，实际并没有覆盖，这种情况有点是rom压缩率高，可写分区就大一些，其次只要备份/overlay就可以把rom以为的所有文件备份下来，以后可以全部还原不用配置了，格式化/overlay分区就相当于恢复Openwrt出厂设置了；

官方推荐squafs，因为这种格式就算配置乱了还可以恢复刷机后的出厂设置，二是压缩后节省空间。jffs2格式搞乱了就只能重刷了；

lzma

LZMA，（Lempel-Ziv-Markov chain-Algorithm的缩写），是一个Deflate和LZ77算法改良和优化后的压缩算法。
