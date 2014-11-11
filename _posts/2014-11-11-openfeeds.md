---
layout: post
title: Openwrt feeds机制 makeclean操作
category: openwrt
---

## feeds机制

In openwrt , a "feed" is a collection of packages which share a common location, Feeds may reside on a remote server, in a version control system, on the local filesystem, orin any other location addressable by a single name over a protocol with a supported feed method，feeds are additional predefined package build recipes for openwrt buildroot.

在openwrt中feed是一系列的软件包，这些软件包需要通过一个统一的接口地址进行访问，feed软件包中的软件可能分布在远程服务器上，在svn上，在本地文件系统中或者其他的地方，用户可以通过一种支持feed机制的协议，通过同一个地址进行访问，简单地说就是系统将一些列的软件包进行了地址映射，通过一个接口进行访问。

我们在下载的openwrt源码是纯净的系统，feeds提供了我们在编译固件的时候需要的许多额外扩展软件，下载open源码之后进行如下操作

./script/feeds update -a && ./script/feeds install -a

从feeds提供的接口地址将Openwrt所需的一些软件先行下载。

## make clean 

make clean
清除bin 目录 bulid_dir 目录

make dirclean
make clean + 清除交叉编译工具以及工具链目录 

make distclean
清除所有相关的东西，包括下载的软件包，配置文件，feed内容等
