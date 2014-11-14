---
layout: post
title: openwrt的Makefile分析
category: openwrt
---

对于嵌入式工程，了解Makefile过程可以提高效率。

主Makefile 可以分为三个部分：前导部分，首次执行部分，再次执行部分。

* 前导部分

  TOPDIR:=${CURDIR}
  LC_ALL:=C
  LANG:=C
  export TOPDIR LC_ALL LANG

  empty:=
  space:= $(empty) %(empty)
  $(if $(findstring $(space),$(TOPDIR)),$(error ERROR: The path to the OpenWrt directory must not include any spaces))

  world:

  include $(TOPDIR)/include/host.mk
  

  CURDIR 默认当前目录，export将 TOPDIR LC_ALL LANG 变量延伸到下层Makefile;
  include部分，在openwrt中多次用了include符，表明Makefile 拆开了多个文件，被拆分的文件放在不同的目录，拆分的目的是明确各部分的功能，增加了灵活性；
  目标world表示：指示当make命令不带目标时所要执行的目标，没有设定依赖和命令部分的目标在此后会有其他依赖关系和命令，world的参考$(TOPDIR)/include/toplevel.mk和主makefile的再次执行部分。

* 首次执行部分

  ifneq ($(OPENWRT_BUILD),1)
    _SINGLE=export MAKEFLAGS=$(space);

	override OPENWRT_BUILD=1
	export OPENWRT_BUILD
	GREP_OPTIONS=
	export GREP_OPTIONS
	include $(TOPDIR)/include/debug.mk
	include $(TOPDIR)/include/depends.mk
	include $(TOPDIR)/include/toplevel.mk
  else
  	include rules.mk
	include $(INCLUDE_DIR)/depends.mk
	include $(INCLUDE_DIR)/subdir.mk
	include target/Makefile
	include package/Makefile
	include tools/Makefile
	include toolchain/Makefile


* 再次执行部分

  引入了rules.mk $(INCLUDE_DIR)/depends.mk, $(INCLUDE_DIR)/subdir.mk target/Makefile package/Makefile tools/Makefile toochain/Makefile，七个文件，在rules.mk定义了INCLUDE_DIR为$(TOPDIR)/include，所以$(INCLUDE_DIR)/depends.mk实际上与首次执行时引入的$(TOPDIR)/include/depends.mk是同一个文件。
  四个子目录下的Makefile不能独立执行，主要利用$(INCLUDE_DIR)/subdir.mk动态建立规则，例如$(toolchain/stamp-install)目标是靠$(INCLUDE_DIR)/subdir.mk的stampfile函数动态建立。在package/Makefile动态建立了$(package/ stamp-prereq)、$(package/ stamp-cleanup)、$(package/ stamp-compile)、$(package/ stamp-install)、$(package/ stamp-rootfs-prepare)目标。
  tmp/.prereq_packages目标是对所需软件包的预处理。目标依赖于.config，即执行make menuconfig后将会进行一次所需软件包的预处理。不知什么原因在编译前删除tmp目录，执行时无法建立tmp/.prereq_packages文件。
  clean是清除编译结果的目标，清除$(BUILD_DIR) $(BIN_DIR) $(BUILD_LOG_DIR)三个目录的用意是十分明确。暂时不知道为什么执行make target/linux/clean。
  dirclean是删除所有编译过程产生的目录和文件的目标，执行dirclean目标依赖于clean，因此将执行clean目标所执行的命令，然后删除$(STAGING_DIR) $(STAGING_DIR_HOST) $(STAGING_DIR_TOOLCHAIN) $(TOOLCHAIN_DIR) $(BUILD_DIR_HOST) $(BUILD_DIR_TOOLCHAIN)目录，以及删除$(TMP_DIR)目录。上述目录的变量均在rules.mk定义。好像删除staging_dir目录就意味着删除staging_dir目录下的所有子目录，不知道为什么要强调删除$(STAGING_DIR_HOST) $(STAGING_DIR_TOOLCHAIN) $(TOOLCHAIN_DIR)目录。同样删除builde_dir目录就意味着删除builde_dir目录下的所有子目录，不知道为什么要强调删除$(BUILD_DIR_TOOLCHAIN)目录。
  prereq应该是预请求目标，在OpenWrt执行Makefile时好像都要先执行prereq目标。
  prepare应该是准备目标，是world依赖的一个伪目标。依赖于文件.config和$(tools/stamp-install) $(toolchain/stamp-install)目标。
   world就是编译的目标。依赖于prepare为目标和前面提到的变量命名目标。采用取消隐含规则方式执行package/index目标。package/index目标在package/Makefile的92行定义。
   package/symlinks和package/symlinks-install是更新或安装软件包来源的目标，使用$(SCRIPT_DIR)/feeds脚本文件完成。
   package/symlinks-clean是清除软件包来源的目标，也是使用$(SCRIPT_DIR)/feeds脚本文件完成。
   最后使用伪目标.PHONY说明clean dirclean prereq prepare world package/symlinks package/symlinks-install package/symlinks-clean属于伪目标。通过伪目标说明可以知道可以执行的目标。
