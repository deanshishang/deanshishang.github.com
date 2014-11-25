---
layout: post
title: openwrt源码移植过程碰到的问题积累
category: openwrt
---

svn co svn://svn.openwrt.org/openwrt/trunk .
openwrt版本 43375 2014/11/25

Makefile文件中有一个函数原型 $(eval text): 它的意思是 text 的内容将作为makefile的一部分而被make解析和执行。

makefile:

* $(eval xd:xd.c a.c) 相当于 cc xd.c a.c -o xd

* define MA

  aa:aa.c

      gcc -g -o aa aa.c

  endef

  $(eval $(call MA))

  相当于 gcc -g -o aa aa.c


源码下载完毕后:
BSDmakefile Config.in rules.mk Makefile LICENSE README 这些文件
target package tools config docs include scripts toolchain  这些目录

* target/linux/<platform> 各个平台相关的代码

  target/linux/<platform>/config-* 各个平台的配置文件

  注： openwrt的平台概念，其支持非常多的target, 制作出的target系统大多数的模块与应用是相同的，Openwrt会把公用的部分提取出来，这样免于重复，又便于bug和改版升级，这样就有了平台的概念，无论是走Openwrt的框架还是Openwrt的产品开发，这个概念很有用；

  举一个简单的例子：
  如果我选择了brcm-2.4的target，linux内核的配置文件由下面两个文件组成
  target/linux/generic-2.4/config-default
  target/linux/brcm-2.4/config-default
  上面是所有使用linux2.4内核的target公共的配置，下面是brcm-2.4 target的
  linux内核的专用配置。openwrt通过Makefile运行相关的perl和shell脚本，将这
  两个配置拼接成target的最 终需要的linux的内核配置。

* package 包含了在配置文件里设定的所有编译好的软件包，有默认选择的软件包；

  使用命令 ./script/feeds/ update -a 来更新所有软件包(设置的)
         
		   ./script/feeds/ search nmap 查找软件包

		   ./script/feeds/ install nmap 安装包

		   make packages/symlinks 更新软件源


* tool以及toolschain 生成固件，编译器和C库的工具，包含一些通用命令，编译产生三个目录

  build_dir/host 临时用来建立target的工具；

  build/toolchain-<arch>* 用来建立特殊结构的toolchain;

  staging_dir/toolchain-<arch>* toolchain的安装目录；

  如果不增加新版本的原件就不需要对toolchain目录进行修改。

* config 存放这整个系统的配置文件；

* docs 不断包含了整个宿主机的文件源码的介绍，里边有makefile为目标系统生成docs

* include 整个系统编译的头文件，用Make进行连接；

* script组织编译整个Openwrt的规则；

目录结构及大小为:

![](/image/trunk1.png)


对源码继续操作 ./script/feeds/ update -a && ./script/feeds/ install -a
再次查看目录结构及大小

![](/image/trunk2.png)

多出来了两个目录 tmp 以及 feeds 前者是编译文件夹，后者是软件包的索引目录；

编译之后还会产生一些其他目录，提前了解如下：

build dir/host 临时目录，用来储存不依赖目标平台的工具；

build dir/toolchain 用来存储依赖于指定平台的编译链，只是编译文件存放目录无需修改；

build dir/target 用来存储依赖于指定平台的软件包的编译文件，其中包括内核Uboot，packages,只是编译文件存放目录无需修改

bin 编译完Openwrt的二进制文件生成目录，其中包括sdk uimage uboot dts rootfs 构建一个完整嵌入式系统的二进制文件；

dl 包含所有编译中需要软件的下载目录；
