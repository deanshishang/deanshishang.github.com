---
layout: post
title: Tig - Norflash,Nandflash,SPIflash,SDRAM对比
category: 技术
tags: Tig
keywords:
description:
---

最近在研究智能路由器的嵌入式开发，通过搜集材料对各种flash进行了一次系统的对比如下：

#### Nor flash

适合小容量的程序或者数据存储，类似小硬盘；

读速度接近于内存，写速度稍慢；

<p>

#### Nand flash

适合大容量数据存储，类似硬盘；

手机 U盘 SSD硬盘上都采用该技术，容量较大，路由器领域应用的少；

<p>

#### SPI flash

串行总线flash，路由器领域使用广泛的存储器，读速度比Nor falsh慢，写速度比Nor flash 快很多；

Nor flash 的一种，接口简单，每次传输一个bit位的数据；

<p>

#### SDRAM

主要用于程序执行时候的程序存储，执行或者计算，类似内存；

<p>

#### DDR SDRAM

双倍速率动态存储器，通过SDRAM升级的之后的；

<p>

#### 对比

* 外置flash按接口分有总线flash，SPI flash, 前者需要MCU有外部总线接口，后者通过SPI口对flash进行读写。前者比后者快，但后者便宜；

* Nor flash有自己的地址和数据总线，可以类似memory的随机访问方式，在上边可以直接跑程序，可以直接做BOOT，启动的时候会将地址映射到0x00上；

* Nand flash是I/O设备，数据，地址，控制线都是共用的，需要软件区域控制读写时序，不像Nor flash一样随机访问，不能EIP(片上运行)，不能直接做BOOT；

#### 不同的flash启动过程

* Nand flash:

*ARM体系结构大部分设备中，Nand 启动，Nand flash控制器将前4K存储复制到stepping stone（内部SRAM缓冲器）,并吧0x0000_0000设置为内部SRAM的起始地址，CPU从内部SRAM的0x0000_0000开始启动，这个过程不需要程序干涉(CPU自动将nand flash的前4K读取放置在片内SRAM里边s3c2440是soc，同时把这段片内SRAM映射到nGCS0片选的空间(0x0000_0000)。CPU从0x0000_0000开始执行，也就是Nand flash的前4K程序，Nand flash地址线都没有，不能直接把Nand映射到0x0000_0000，只好用片内SRAM做为载体，通过这个载体把Nand flash的大代码复制到RAM(一般是SDRAM)中去执行）。程序员的工作就是制作最核心的代码放在前4K中，要完成S3C2440的核心配置以及启动代码(U-BOOT)的剩余部分拷贝到SDRAM中，这4K的代码需要将NAND flash的内容复制到SDRAM中执行，前4K放启动代码，SDRAM速度快，用来执行主程序的代码，ARM一般从ROM或者Flash启动完成初始化，然后应用程序拷贝到RAM，跳到RAM执行;*

* Nor flash:

*支持代码直接放到NOR flash上执行，无需复制到内存当中，这是因为NOR flash的接口与RAM的相同，可以直接随机对内容访问，可以像内存一样操作，但是写擦处效率很低，一般在代码的开始部分汇编指令初始化外部内存部件(SDRAM), 最后跳到外存中继续执行，对于一般的小程序，一般将其放到Nand flash中，借助CPU内部的SRAM直接运行；*

*Nor flash的地址被映射到0x0000_0000中(nGCS0,这里不需要片内SRAM辅助，所以片内SRAM的起始地址还是0x4000_0000)然后CPU从0x0000_0000开始执行(就是在Nor flash中执行)；*

* 为什么有两中启动方式，还是因为两种flash的不同特点造成的，Nor flash容量小，速度快，稳定性好，输入地址，然后给出写信号可从数据口得到数据，适合做程序存储器，nand flash总容量大，但是读写都需要复杂的时序，更适合做数据存储器，这种不同造就了Nor flash可以直接链接到ARM的总线并且可以运行程序，而Nand flash必须搬移到内存(SDRAM)中执行；

* 在路由器的开发中，一般将bootloader烧写到nor flash，程序可以通过串口交互，进行操作，这样方便调试，Nor flash中的bootloader可以烧录内核到Nor flash等等功能；
