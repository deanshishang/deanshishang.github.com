---
layout: post
title: 编译的固件提示You cannot use older JFFS2 filesystems with newer kernels等问题的解决
category: openwrt
---

最近的一个星期在给新的路由器设备适配openwrt系统，有一个问题困扰了很久，先贴代码：



[    0.680000] m25p80 spi0.0: found w25q128, expected m25p80

[    0.680000] m25p80 spi0.0: w25q128 (16384 Kbytes)

[    0.690000] 5 cmdlinepart partitions found on MTD device spi0.0

[    0.690000] Creating 5 MTD partitions on "spi0.0":

[    0.700000] 0x000000000000-0x000000040000 : "u-boot"

[    0.710000] 0x000000040000-0x000000140000 : "kernel"

[    0.710000] 0x000000140000-0x000000ff0000 : "rootfs"

[    0.720000] mtd: partition "rootfs" set to be root filesystem

[    0.730000] split_squashfs: no squashfs found in "spi0.0"                 

[    0.730000] 0x000000ff0000-0x000001000000 : "art"

[    0.740000] 0x000000050000-0x000001000000 : "firmware"

[    0.760000] libphy: ag71xx_mdio: probed

[    0.770000] libphy: ag71xx_mdio: probed

[    0.780000] eth0: Atheros AG71xx at 0xb9000000, irq 4, mode:RGMII

[    1.330000] ag71xx ag71xx.0 eth0: no PHY found with phy_mask=00000001

[    1.340000] eth0: Atheros AG71xx at 0xba000000, irq 5, mode:GMII

[    1.900000] eth0: Found an AR934X built-in switch

[    2.930000] u32 classifier

[    2.930000]     Performance counters on

[    2.930000]     input device check on

[    2.940000]     Actions configured

[    2.940000] TCP: cubic registered

[    2.940000] NET: Registered protocol family 17

[    2.950000] 8021q: 802.1Q VLAN Support v1.8

[    2.960000] jffs2: jffs2_scan_eraseblock(): Magic bitmask 0x1985 not found at 0x00000000: 0x6d9d instead

[    2.970000] jffs2: jffs2_scan_eraseblock(): Magic bitmask 0x1985 not found at 0x00000004: 0x53f5 instead

[    2.980000] jffs2: jffs2_scan_eraseblock(): Magic bitmask 0x1985 not found at 0x00000008: 0x09e9 instead

[    2.990000] jffs2: jffs2_scan_eraseblock(): Magic bitmask 0x1985 not found at 0x0000000c: 0x97af instead

[    3.000000] jffs2: jffs2_scan_eraseblock(): Magic bitmask 0x1985 not found at 0x00000010: 0xc87f instead

….

[   10.230000] jffs2: Further such events for this erase block will not be printed

[   10.250000] jffs2: Old JFFS2 bitmask found at 0x00335b9c             

[   10.250000] jffs2: You cannot use older JFFS2 filesystems with newer kernels  

[   10.280000] jffs2_scan_eraseblock(): End of filesystem marker found at 0x340000
j

[   10.290000] jffs2: Cowardly refusing to erase blocks on filesystem with no valid JFFS2 nodes
j

[   10.300000] jffs2: empty_blocks 183, bad_blocks 0, c->nr_blocks 235

[   10.300000] VFS: Cannot open root device "(null)" or unknown-block(31,2): error -5

[   10.310000] Please append a correct "root=" boot option; here are the available partitions:

[   10.320000] 1f00             256 mtdblock0  (driver?)

[   10.330000] 1f01            1024 mtdblock1  (driver?)

[   10.330000] 1f02           15040 mtdblock2  (driver?)

[   10.340000] 1f03              64 mtdblock3  (driver?)

[   10.340000] 1f04           16064 mtdblock4  (driver?)

[   10.350000] Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(31,2) 



我按照提示，修改了target/linux/ar71xx/image/Makefile文件，目的是修正spi flash分区信息，本来是8M的flash，我换了一个spi flash为16M的，出现问题的上边代码中，Makefile文件中相关代码是：



256K uboot--64K uboot-env--1024K kernel 

问题解决后的相关代码是：

128K uboot--1024K kernel

首先分析，解压内核的步骤是bootm 0x9f020000，每次我cp.b烧写的固件位置都是在这里，出现问题的原因是，我自己makefile的spiflash对应的kernel是从偏移量uboot256K+ubootenv64K也就是0x9f050000开始的，如果我把固件烧写到0x9f020000就不对应spiflash的分区信息了，所以第一如果要是我直接烧写到0x9f050000的话，直接bootm0x9f050000就能启动系统了，但是uboot信息中bootm 默认是0x9f020000，所以我将uboot大小改成了128K（uboot事实上就是这个大小），然后去掉ubootenv，这样spiflash分区信息就与我的设置对应上了；

至此问题解决！

接下来设备能够启动了，但是总是会重新启动，找不到原因，继续搞！

提示 mount_root jffs2 is not ready - marker found.....
