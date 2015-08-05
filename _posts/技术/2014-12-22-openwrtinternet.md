---
layout: post
title: openwrt无线连接互联网实现原理，mesh网络基本原理与openwrt应用
category: 技术
tags: Openwrt
keywords:
description:
---

#### openwrt系统中的network与wireless文件

* /etc/config/network

```
config interface lan
	option ifname "eth1"
	option proto "static"
	option type "bridge"
	option ipaddr "192.168.1.1"
	option netmask "255.255.255.0"

config interface wan
	option ifname "eth0"
	option proto "dhcp"
```


* /etc/config/wireless

```
config wifi-device  radio0
	option type     mac80211
	option channel  11
	option hwmode	11ng
	option path	'platform/ar933x_wmac'
	option htmode	HT20
	list ht_capab	SHORT-GI-20
	list ht_capab	SHORT-GI-40
	list ht_capab	RX-STBC1
	list ht_capab	DSSS_CCK-40
	# REMOVE THIS LINE TO ENABLE WIFI:
	option disabled 0

config wifi-iface
	option device   radio0
	option network  lan
	option mode     ap
	option ssid     ERYA_09A488
	option encryption none
```

#### 联网实现原理

DD中的*repeater bridge* 联网方式,在openwrt中称为*routed client mode masquerade*,实现原理是这样的：

openwrt的网络分为:</br>
*device --> interface --> network --> zone*

device就是例如无线网卡radio0这种物理设备,interface为例如wlan0这种通过无线网卡驱动虚拟出来的设备接口，这个虚拟出来的接口可以做ap也就是发射无线信号，称为sta(station)，network是OpenWRT对于interface的一个分组。比如你有eth1, eth2, eth3, eth4四个独立的有线网卡提供的interface，但是设置了一个bridge把这四个独立的interface桥接到了一起，然后它们都从属于一个network。在network上配置的是对于network内的interface怎么获取ip。zone是OpenWRT为了方便管理iptables规则建立的更高层次的抽象。zone可以包含一个或者多个network，定义了iptables规则是否要在不同zone之间做forward还是masquerade。masquerade就是所谓的NAT（NAPT），也就是用一个外网ip给多个内网客户端提供上外网的服务，每次连接都会分配一个端口用于让连接的下行流量可以映射回内网的客户端。

无线连接互联网其实就是有两个zone，一个叫lan，一个叫wan。然后设置了从lan到wan的masquerade规则，这样所有连接到lan的机器都可以通过wan NAT出去。

然后在lan这个zone下面有只有一个network，名字也叫lan。wan这个zone下面也只有一个network，名字叫wan。

同时由于dhcp的配置（/etc/config/dhcp）的配置中打开了lan的dhcp。所以所有连接进lan的机器都会被dhcp分配一个192.168.1.x网段的ip，而路由器自身的ip被静态设置为了192.168.1.1。wan口因为是用无线连接进上级无线的网络，所以ip是通过dhcp由上级无线分配的。

在network下面是一个或者多个interface。在我们这个例子中，lan下面的interface有两个，一个是有线网口eth1，另外一个是无线名称是的无线wlan0。wan下面的interface只有一个就是连接着上级无线的wlan0-1。有线网口interface配置是与network在一起的，前面的lan network的ifname eth1就是有限网口的配置了。无线网的interface都是在/etc/config/wireless中配置的;

wifi-iface就是我们配置的无线interface。其中一个ssid是fqrouter-703n的无线interface属于network lan。另外一个ssid是你上级无线的那个无线interface属于network wan。而两个interface的device都是radio0，所以都是同一个无线设备虚拟出来的。
