---
layout: post
title: The Openwrt Flash Layout
category: 技术
tags: Openwrt
keywords:
description:
---

	http://wiki.openwrt.org/doc/techref/flash.layout


  Most routers do not have hard drives. They use flash memory for similar purpose: storing programs and data, even when the system is off.

  In most system, flash memory does not appear like RAM and so data and instructions must be copied to RAM to be used. So, for example, the bootloader copies the kernel from flash to RAM and then starts that copy running.

  There are two basic types of flash memory: NOR flash and NAND flash, Older routers generally have raw NOR flash but many newer routers have raw NAND flash.

  The following table gives you a visual of the layout of the TL-WR1043ND and is merely on example!

![](/image/flashlay1.png)

##### Partitions

Since the partitions are nested 嵌套 we look at whis whole thing in layers:

1, Layer0: So we have the Flashchip, 8M in size , which is soldered 焊接 to the PCB and connected to the Soc over SPI.

2, Layer1: We "partition" the space into mtd0 for the bootloader, mtd5 for the firmware and, in this case, mtd4 for the ART(Atheros Radio Test)-it contains calibration data for the wifi. If it is missing or corrupt, ath9k won't come up anymore, The bootloader contais of the u-boot 64K block AND a data section which contains the MAC, WPS-PIN, and type description. If no mac is configured wifi will not work correctly due to a faulty MAC.

3, Layer2:  we subdivide mtd5 (firmware) into mtd1 (kernel) and mtd2 (rootfs); In the generation process of the firmware (see obtain.firmware.generate) the Kernel binary file is first packed with LZMA, then the obtained file is packed with gzip and then this file will be written onto the raw flash (mtd1) without being part of any filesystem!

4, Layer3: we subdivide rootfs even further into mtd3 for rootfs_data and the rest for an unnamed partition which will accommodate the SquashFS-partition.

##### Mount Points

1, / this is your entire root filesystem, it comprises /rom and /overlay, igbore /rom and /overlay and use exlcusively / for your daily routines!

2, /rom contains all the basic files like busybox dropbear or iptables. It also includes default configuration files used when booting into Openwrtfailsafemode, it does not contain the linux kernel. All files in this directory are located on the SqashFS partition and thus cannot be altered or deleted. But, because we use overlay_fs flisystem, so called overlay-whiteout-symlinks can be created on the JFFS2 partion 

3, /overlay is the writable part of the file system that gets merged with /rom to creat a uniform /- tree it contains anything that was written to the router after installation, e.g.changed configuration files ,additional packages installed with opkg. etcit is formated with jffs2!

Whenever the system is asked to look for an existing file in /, it first look in /overlay, and if not there, then in /rom. In this way /overlay overrides /rom and creates the effect of a writable / while much of the content is safely and efficiently stored in the read-only /rom.

When the system is asked to delete a file that is in /rom, it instead creates a corresponding entry in /overlay, a whiteout. A whiteout is a symlink to that mostly behaves like a file that doesn't exist.
