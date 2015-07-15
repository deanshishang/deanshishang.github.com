---
layout: post
title: Linux防火墙与NAT
category: Note
---

防火墙最大的功能就是帮助你限制某些服务的存取来源，举例来说：

* 可以限制ftp服务只有子网内的主机才能使用，而不是整个internet开放；

* 可以限制整部主机仅可以接受客户端的www要求，其他服务都关闭；

* 还可以限制整部主机仅能主动对外界连线，所有对我们自己主机发送的都丢掉；

防火墙最重要的任务：规划出被信任与不被信任的网段，划分出可以提供Internet服务与必须受保护的服务，分析出可接受与不可接受的封包状态；

### linux系统上防火墙的类别

防火墙分为区域型和单一主机型控管，单一管控方向，主要的防火墙封包过滤型的Netfiler与依据服务软体程式作为分析的TCPWrappers 两种，若以区域型的防火墙而言，由于此类防火墙都是当作路由器的角色，因此防火墙类型主要则有封包过滤的Netfilter与利用代理伺服器(proxy server)进行存取代理的方式；

* Netfilter:这种方式可以直接分析封包表头资料，所以包括MAC,ip,tcp,udp,icmp等封包都可以进行过滤分析的功能，主要是OSI的234层，非常适合小型的设定，决定什么包接收什么包丢掉，达到保护主机的目的；

* 另一种抵挡封包进入的方式，为透过伺服器程式的外挂(tcpd)来处置的，这种机制主要分析谁对某程式进行存取，然后透过规则去分析该伺服器程式谁能够连线，谁不能连线，主要是通过分析伺服器程式控管，因此与启动的端口无关，举例来说，我们知道ftp可以启动在非正规的21进行监听，当你通过linux内建的tcp wrappers限制ftp时，那么你只要知道ftp的软件名称，vsftpd，然后对他做限制，则不管ftp启动在哪个端口，都会被规则管理的。

* Proxy(代理伺服器)：代理伺服器就是一种网络服务，可以代理使用者的要求，而代为前往伺服器取得相关的资料，client没有直接连上internet，所以在代理与client之间连线就可以了，client并不需要public ip甚至，当有人要攻击client端的主机时，除非能够攻破proxy server，否则无法与client连线！一般proxy主机通常仅开放port 80 21 20,等 与ftp的端口，通常就假设在路由器上边，因此可以完整的掌控区域网络内的对外连线，让lan变得更安全；


整理来自<a href="http://linux.vbird.org/linux_server/0250simple_firewall.php#nat_what">Linux防火墙与NAT伺服器</a>