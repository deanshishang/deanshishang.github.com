---
layout: post
title: Tig - openwrt添加新的路由器支持以及补丁工具quilt 的使用
category: 技术
tags: Openwrt
keywords:
description:
---


**手里有厂商提供了一块板子什么信息基本都没有，开始考虑自己新增一个配置到openwrt的列表。**

如果想把openwrt移植到列表中没有的路由器上，目标是新增target system platform，首先可以增加一个target profile。

比如说新的路由器名字叫做DEAN-ERYA100 配置是AR9341 + nor 16M + ram 64M，ar9341这个平台中即为atheros Ar7XXX/Ar9XXX，要做的就是在Openwrtmenuconfig中加入DEAN-ERYA100 的支持。

```
1，首先加入Dean-ERYA100 的宏定义
tools/firmware-utils/src/mktplinkfw.c

#define HWID_DEAN_ERYA100_V1 0x08100001
#define HWID_DEAN_ERYA100_V2 0x08100002

2，board的属性
tools/firmware-utils/src/mktplinkfw.c

{
	.id     = "DEAN-ERYA100V1",
	.hw_id      = HWID_DEAN_ERYA100_V1,
	.hw_rev     = 1,
	.layout_id  = "16Mlzma",
}, {
	 .id     = "DEAN-ERYA100V2",
	 .hw_id      = HWID_DEAN_ERYA100_V2,
	 .hw_rev     = 1,
	 .layout_id  = "16Mlzma",
}, {
	 .id     = "DEAN-ERYA100",
	 .hw_id      = HWID_DEAN_ERYA100_V1,
	 .hw_rev     = 1,
	 .layout_id  = "16Mlzma",
}

以上内容是在static struct board_info boards[]中添加的。

3，添加镜像生成命令
target/linux/ar71xx/image/Makefile


$(eval $(call SingleProfile,TPLINK-LZMA,$(fs_64kraw),DEANERYA100V1,dean-erya100-v1,DEAN-ERYA100,ttyS0,115200,0x08100001,1,16Mlzma))
$(eval $(call SingleProfile,TPLINK-LZMA,$(fs_64kraw),DEANERYA100V2,dean-erya100-v2,DEAN-ERYA100,ttyS0,115200,0x08100001,1,16Mlzma))

$(eval $(call MultiProfile,DEANERYA100,DEANERYA100V1,DEANERYA100V2))

4，添加menuconfig菜单

target/linux/ar71xx/generic/profiles/tp-link.mk 添加Kconfig定义如下


define Profile/DEANERYA100
    NAME:=ERYA DEAN-ERYA100
	PACKAGES:=
endef

define Profile/DEANERYA100/Description
	Package set optimized for the ERYA DEAN-ERYA100.
endef

$(eval $(call Profile,DEANERYA100))

其中define Profile/ERYADEAN100是选项CONFIG宏，CONFIG_TARGET-ar71xx_generic_DEANERYA100,ar71xx是target_system名字，generic是subtarget名，NAME是现实在MENUCONFIG中的选项名；

5，删除tmp/目录，

因为make menuconfig依赖于tmp/.config-target.in，不存在这个文件会新生成。

上边步骤完成之后，make menuconfig，找到target profile下边，显示如下：

![](/image/new1.png)

```

```
make V=s99 编译之后，bin/ar71xx下生成factory.bin文件，但是这个下载到flash不能启动，因为内核没有支持erya1000，接下来就是 kernel arch machine 新增路由器。

Kernel增加deanerya100的支持
build_dir/target-mips_34kc_uClibc-0.9.33.2/linux-ar71xx_generic/linux-3.10.58

1，新增archmachine，拷贝/arch/mips/ath79/mach-tl-wr841n-v8.c 到mach-dean-erya100.c，就是改了个名称，给__mips_machines_start挂一个machine节点。
修改/arch/mips/ath79/machtypes.h enum ath79_mach_type{},最后新增ATH_79_MACH_DEAN_ERYA100定义。

2，kernel_menuconfig支持
/arch/mips/ath79/Kconfig增加

config ATH79_MACH_DEAN_ERYA100
    bool "DEAN-ERYA100 V1/2 support"
	select SOC_AR934X
	select ATH79_DEV_ETH
	select ATH79_DEV_GPIO_BUTTONS
	select ATH79_DEV_LEDS_GPIO
	select ATH79_DEV_M25P80
	select ATH79_DEV_WMAC

arch/mips/ath79/Makefile新增
obj-$(CONFIG_ATH79_MACH_DEAN_ERYA100)  += mach-dean-erya100.o

3, make kernel_menuconfig 
在machine的selection里边就可以找到设备选择了

那么问题来了？makeclean之后怎么办，每次重新编译都得手写代码？
quilt来了，上边的修改都可以合入到target/linux/path-3.XXX下边kernel补丁文件，最好办法是直接修改补丁，添加支持dean-erya100。

```

### quilt工具的使用

openwrt kernel源码修改，一旦Makeclean之后呢，kernel源码就会删掉了，下次得重新修改，弄懂quilt使用借助它就可以自动修改和添加 kernel patch

1，quilt的安装

```
	openwrt编译过程自动下载quilt.tar.gz，拷贝到目录下加压缩之后，安装到主机上；修改配置文件：
	vi ~/.quiltrc

	 QUILT_DIFF_ARGS="--no-timestamps --no-index -pab --color=auto"
	 QUILT_REFRESH_ARGS="--no-timestamps --no-index -pab"
	 QUILT_PATCH_OPTS="--unified"
	 QUILT_DIFF_OPTS="-p"
	 EDITOR="vim "
```
```
2，解压缩kernel

	openwrt下make dirclean,清除掉kernel的源码，然后make kernel_menuconfig重新解压缩一个原始的openwrt kernel;

	进入kernel 源码目录，cd build_dir/targetXXX/linux-ar71xx_generic/linux-3.XXX/;

	此时的Kernel已经被openwrt打上了kernel根目录下patches/一堆patch，列表可以cat series;

	输入quilt top 可以得到最后一条patch文件；

![](/image/quilt.png)
```

3，修改kernel patch文件

```
	新增patch文件：

![](/image/quilt1.png)

	此时这个patch就会认为是顶部的patch并且针对kernel的一切修改都会自动diff到这个patch文件中了；

	然后修改文件(上边步骤中的修改Kconfig示范)，并且更新，quilt refresh；

![](/image/quilt2.png)

	这样保存之后把所有修改都添加到这个patch文件中了，可以查看修改内容如下：

![](/image/quilt3.png)
```

4，同步openwrt将新增的patch文件拷贝到Openwrt，target/linux/ar71xx/patches-XX/针对kernel的修改就不会再丢失了，补丁会在下次解压缩openwrt的源码时候自动打上。

可参考一个网上的例子，quilt使用的例子 
[quilt](http://blog.csdn.net/hbsong75/article/details/8825184 "quilt的使用")
