---
layout: post
title: 内存和Flash的区别
category: Tag
---

ROM与RAM都是半导体存储器；

* Rom是Read Only Menory的缩写，一旦存储就无法将之改变；

* Ram是Random Access Memory的缩写，随机存储器，断电的时候将丢失内容存储，故主要用于存储短的使用程序；RAM分为两大类，一类是静态RAM即SRAM，速度非常快，用于CPU的一级二级缓存，另外一种是动态RAM即DRAM，保留数据时间很短，速度比SRAM慢，内存就是DRAM；典型的一种是DDR ROM 即DATE-RATE RAM 不同之处在于可以一个时钟读写两次数据，这样就可以使传输速度加倍了，目前电脑中用的最多的内存，而且它有着成本优势，可以提高带宽；

* 典型的RAM就是计算机的内存；

* 例子：手机软件放在EEPROM中，打电话最后拨打的那些号码。暂时是SRAM中得，不是马上写入通话记录；

* FLASH存储器称为闪存，结合了RAM与ROM的长处，具备电子可擦除可编程的功能，还不会断电地市数据，嵌入式系统过去使用ROM，近年来使用FLASH全面代替了ROM，用作存储bootloader以及操作系统或者程序代码直接当硬盘使用；

* FLASH主要由 Norflash 与 Nandflash，前者读取和常见的SDRAM是一样的，用户可以直接运行装载在Norflash里边的代码，这样可以减少SRAM容量节约成本；后者没有采取内存的随机读取技术，是以一次读取u一块的形式来进行的，通常是一次读取512个字节，采用这种技术的flash比较廉价，用户不能直接运行nand flash上的代码，好多使用nandflash的开发板除了使用nand以外，还做上了一块小的norflash 来运行启动代码；

* 一般小容量的用norflash，因为其读写速度快，多用来存储操作系统等重要信息，大容量的用nand flash，最常见的是nandflash 应用嵌入式系统的DOC和我们通常用的闪盘，可以在线擦除，最常见的flash来自inter amd等；


