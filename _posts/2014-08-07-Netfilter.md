---
layout: post
title: Openwrt防火墙配置结构
category: Openwrt
---

Openwrt的防火墙和其他Gnu/Linux的发行版本一样也是基于iptables，配置防火墙有两种方式，一种是自己的uci方式，另一种基于Linux方式；

uci的方式就是通过配置/etc/config/firewall 这个文件来完成；

### firewall 的文件结构

* defaults：这是firewall文件的第一个小节;

* zone：可以有多个zone，zone又可以包含数个network interface；

* forwarding：在zone下边允许数据封包转发；

* rule以及redirect：可以看做zone的子集，来扩展进一步的封包限制；

### 每个小节

最小的防火墙通常包含一个default节，两个zone(lan,wan)，和一个forwarding允许数据包由lan转发到wan；

#### defaults 不依赖于特定区域的全局设置

syn_flood: 允许syn-flood保护；

drop_invalid：丢弃任何没有匹配到已有连接的包；

disable_ipv6：禁用IPv6防火墙设置；

input：accept drop reject；

forward：accept drop reject;

output：accept drop reject;

#### zone

input rules for a zone describe what happens to traffic trying to reach the router itself through that interface;

output rules for a zone describe what happens to traffic originating from the router itself;

forward rules for a zone describe what happens to traffic coming from that zone and passing to another zone.

![](/image/iptables1.png)

#### forwading

![](/image/iptables2.png)

### redirects

port forwarding(端口映射) are defined by redirect sections, all incoming traffic on the specified source zone which matches the given rules will be directed to the specified internal host.

redirects are also commonly known as port forwarding and virtual servers.(重定向和虚拟服务器)

![](/image/iptables3.png)

#### rules

这部分的可以用来定义基本的接受或者拒绝的规则，允许或限制访问特定的端口或主机；

![](/image/iptables4.png)


### iptbles的动作

* ACCEPT 一旦包满足了制定的匹配条件，就会被ACCEPT，并且不会再去匹配当前链中得其他规则或者同一个表内的其他规则，但是它还是要通过其他表中的链；

* DROP 如果包含符合条件，这个target就会把它丢掉，包生命结束，不再往前继续走，包阻塞了，某些情况下，这个target会引起意外的结果，因为不会向发送者返回任何信息，也不会由路由器返回信息，这就可能会使连接的另一方socket因苦等回音而亡；解决这个问题的方法是使用REJECT target；

* REJECT和DROP基本一样，区别在于它除了阻塞包之外，还返回发送者错误信息，此target只能用在INPUT,OUTPUT,FORWARD，和他们的子链里，包含REJECT的链只能被他们调用，否则不能发挥作用，只有一个选项，用来控制返回的错误信息的种类的；




##### <a href="http://wiki.openwrt.org/zh-cn/doc/uci/firewall?s[]=hotplug">参考文档</a>



