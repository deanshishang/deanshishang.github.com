---
layout: post
title: openwrt添加新的路由器支持以及quilt的使用
category: openwrt
---

手里有厂商提供了一块板子什么信息基本都没有，开始考虑自己新增一个配置到openwrt的列表。

如果想把openwrt移植到列表中没有的路由器上，目标是新增target system platform，先可以增加一个target profile。

比如说新的路由器名字叫做DEAN-ERYA100配置是AR9341 + nor 16M + ram 64M，ar9341这个平台中即为atheros Ar7XXX/Ar9XXX，要做的就是在Openwrtmenuconfig中加入DEAN-ERYA100的支持。

1，首先加入Dean-ERYA100的宏定义
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


