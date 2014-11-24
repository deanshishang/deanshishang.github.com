---
layout: post
title: openwrt使用的文件系统jffs2与squashfs, LZMA
category: openwrt
---

jffs2文件系统格式适合于断电系统，不像FAT那样容易丢失文件，因为路由器都容易突然断电；

官方的jffs2格式刷到路由器就是一个jffs2分区，ROM本身和以后安装的软件都在这个区里可以读写；

squashfs是把rom压缩到一个文件刷金路由器，然后剩下的空间格式化成jffs2并且优于ROM文件，rom的只读分区挂载到/rom下，另一个读写jffs2分区挂到/overlay,当两个目录下有同名文件就优先读这个，相当于覆盖了rom文件，实际并没有覆盖，这种情况有点是rom压缩率高，可写分区就大一些，其次只要备份/overlay就可以把rom以为的所有文件备份下来，以后可以全部还原不用配置了，格式化/overlay分区就相当于恢复Openwrt出厂设置了；

官方推荐squafs，因为这种格式就算配置乱了还可以恢复刷机后的出厂设置，二是压缩后节省空间。jffs2格式搞乱了就只能重刷了；

lzma

LZMA，（Lempel-Ziv-Markov chain-Algorithm的缩写），是一个Deflate和LZ77算法改良和优化后的压缩算法。
