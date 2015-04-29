---
layout: post
title: 实战之iptables规则说明与使用
category: Note
---

# netfilter/iptables 可以对流入或者流出的信息进行细化控制，实现包过滤功能的应用程序
###包含在2.4以后的内核中，可以实现防火墙，NAT，数据包分割的功能，netfilter包含在内核之中，iptables可以提供用户规则的定制，iptables从ipchains和ipwadfm(IP防火墙管理)演化而来，功能更加强大；


### 内核模块可以注册一个新的规则表(table)，并要求数据包经过指定的规则表，这种数据包选择用于实现数据包过滤表(filter表)，网络地址转换(nat表)，数据报处理表(mangle表)，这三种数据包处理功能都是基于netfilter的钩子函数与IP表，都是相互之间独立的模块，完美的集成到了由netfilter组成的框架中；



                             prerouting(DNAT)    router    filter     postrouting



                               |                                          |
                             input                                     output     

###iptables表:

* `filter:` 用于过滤时
* `nat:` 用于nat时

###iptables链:

* `INPUT:` 用于filter表，匹配目的IP是本机的数据包
* `FORWAD:`用于filter表，匹配穿过本机的数据包


* `PREROUTING:` 用于nat表，用来改变目的地址(DNAT)
* `POSTROUTING：`用于nat表，用来改变源地址(SNAT)

###Forward链、Input链和Output链的区别如下：

* 如果数据包的目的地址是本机，则系统将数据包送往Input链。如果通过规则检查，则该包被发给相应的本地进程处理；如果没有通过规则检查，系统就会将这个包丢掉。
* 如果数据包的目的地址不是本机，也就是说，这个包将被转发，则系统将数据包送往Forward链。如果通过规则检查，则该包被发给相应的本地进程处理; 如果没有通过规则检查，系统就会将这个包丢掉。
* 如果数据包是由本地系统进程产生的，则系统将其送往Output链。如果通过规则检查，则该包被发给相应的本地进程处理；如果没有通过规则检查，系统就会将这个包丢掉。

###iptables语法：

`iptables -t filter/nat <操作命令> [要操作的链] [规则号码] [匹配条件]   [-j 匹配到以后的执行操作]`

####查看命令：-[nvx]L
* iptables -L  列出filter表的所有链和规则
* iptables -t nat -L 列出nat表的所有链和规则
* -v 显示详细信息，包括每条规则匹配报数量和匹配字节数；
* -x 基于-v 禁止单位自动换算；
* -n 只是显示Ip地址，不显示域名和服务器名称；


####操作命令：
* -I(插入一条规则放在最前)   <链名> [规则序号]
* -A(增加一条规则放在最后)  <链名> 
* -P(设置某个链的默认规则)  <链名> <动作>
* IPTABLES默认规则是清理不掉的，需要时改成accept就可以了
 * iptables -t filter -P INPUT ACCEPT
 * iptables -t filter -P OUTPUT ACCEPT
 * iptables -t filter -P FORWARD ACCEP
 * 当数据包没有被规则列表里的任何规则匹配到时，按此默认规则处理
* -D(删除某个规则)              <链名> [规则序号|具体规划内容，根据内容匹配]
* -R(replace代替某个规则)   <链名> <规则序号> <替换的规则>
* -F(FLUSH,清空规则)     <链名>   清空规则
 * 清空链路中的规则，但是不动默认-P设置的规则；
 * -P默认设置的规则为DROP的话，使用-F一定要小心；
 * 如果不写链名，默认清空某表里所有链的里的所有规则；


####例子：


* iptables -t filter -A INPUT -j DROP  (追加一条规则放到最后) 匹配所有访问本机的IP数据包,匹配到的丢弃 

* iptables -t filter -I INPUT 3 -j DROP (添加一条规则插入到第三条)

* iptables -t filter -D INPUT 3 (删除filter表INPUT中的第三条规则，不管是什么) 

* iptables -t filter -D -s 192.168.0.1 -j DROP (删除filter表链INPUT中内容为-s 192.168.0.1 -j DROP的规则，不管位置在哪里)

* iptables -t filter -R INPUT 3 -j ACCEPT (将原来规则为INPUT链中的第三条规则替换为-j ACCEPT)

* iptables -t filter -P INPUT DROP (设置filter表中INPUT链默认规则是DROP)

* iptables -t filter -F INPUT (清空filter表中INPUT链的所有规则)

* iptables -t nat -F PREROUTING (清空nat表中PREROUTING链的所有规则)


#### 匹配条件

* 流出流出接口 (-i -o)
* 来源目的地址 (-s -d)
* 来源目的端口 (--sport --dport)
* 协议类型 (-p)


* -i eth0 
* -i ppp0 匹配数据进入的网络接口


* -o eth0
* -o ppp0 匹配数据流出的网络接口


* -s 192.168.111.1 匹配从源IP地址发来的数据包
* -s 192.168.111.0/24


* -d 192.168.111.1 匹配去往192.168.111.1的数据包
* -d www.abc.com 匹配去往www.abc.com的数据包


* -p tcp  匹配协议类型
* -p udp
* -p icmp --icmp-type


* --sport 1000
* --sport 1000:3000
* --sport :3000
* --sport 1000: (源端口1000以上的)


* --dport 1000


* --sport --dport 必须配合-p参数使用


#### 实例


* 匹配目的端口是53的udp协议数据包
 * -p udp --dport 53


* 匹配来自10.1.0.0/24去往172.2.2.0/24的数据包
 * -s 10.1.0.0/24 -d 172.2.2.0/24


* 匹配来自192.168.0.1去往www.abc.com的80端口的TCP数据包
 * -s 192.168.0.1 -d www.abc.com -p tcp --dport 80 


#### 注意


* --dport --sport一定要配合-p指定协议使用
* 条件写的越多，匹配越细致，匹配越精确


#### 动作

* -j


* SNAT nat表的POSTROUTING链 
 * -j SNAT --to IP[-IP][:端口-端口]
 * 源地址转换，SNAT支持转换为单IP地址或者地址池
* DNAT nat表的PREROUTING链 
* ACCEPT 允许数据包通过本链而不拦截它
* DROP  阻止数据包通过本链并丢弃它
* MASQUERADE 动态源地址转换(动态IP的情况下使用)


* 将源地址是192.168.111.0/24的源地址进行地址伪装
 * iptables -t nat -A POSTROUTING -s 191.168,111.0/24 -j MASQUERADE

* 将内网地址为192.168.111.0/24的源地址修改为1.1.1.1，用于NAT
 * iptables -t nat -A POSTROUTING -s 192.168.111.0/24 -j SNAT --to 1.1.1.1


* 把从ppp0进来的用来访问tcp 80的数据包目的地址转换为192.16.0.1
 * iptbles -t nat -A PREROUTING -i ppp0 -p tcp --dport 80 -j DNAT --to 192.16.0.1


####附加模块

* -m

* state 按包状态匹配 -m state --state 状态 状态:NEW、RELATED、ESTABLISHED、INVALID

 * NEW:有别于 tcp 的 syn
 * ESTABLISHED:连接态
 * RELATED:衍生态,与 conntrack 关联(FTP) INVALID:不能被识别属于哪个连接或没有任何状态
   * 例如: iptables -A INPUT -m state --state RELATED,ESTABLISHED \ -j ACCEPT 


* mac 按源mac匹配

 * -m mac --mac-source MAC 匹配某个 MAC 地址


 * 例如:
   * iptables -A FORWARD -m --mac-source xx:xx:xx:xx:xx:xx \ -j DROP    阻断来自某 MAC 地址的数据包,通过本机 


 * 注意MAC 地址不过路由,不要试图去匹配路由后面的某个 MAC 地址 


* limit 按包速率匹配

 * -m limit --limit 匹配速率 [--burst 缓冲数量]
 * 用一定速率去匹配数据包例如:
   * iptables -A FORWARD -d 192.168.0.1 -m limit --limit 50/s \ -j ACCEPT
   * iptables -A FORWARD -d 192.168.0.1 -j DROP 
 * 注意:
   * limit 仅仅是用一定的速率去匹配数据包,并非 “限制” 

* multiport 多端口匹配

 * -m multiport <--sports|--dports|--ports> 端口 1[,端口 2,..,端口 n] 一次性匹配多个端口,可以区分源端口,目的端口或不指定端口
 * 例如:
   * iptables -A INPUT -p tcp -m multiports --ports \ 21,22,25,80,110 -j ACCEPT

 * 注意: 必须与 -p 参数一起使用 





### 三大“纪律”五项“注意”

####三大“纪律”——专表专用

 * filter 
 * nat
 * mangle
 
####五项“注意”——注意数据包的走向 PREROUTING

 * INPUT
 * FORWARD
 * OUTPUT POSTROUTING
 
### 其他注意事项

* 养成好的习惯
 * iptables -vnL iptables -t nat -vnL iptables-save
* 注意逻辑顺序
 * iptables -A INPUT -p tcp --dport xxx -j ACCEPT iptables -I INPUT -p tcp --dport yyy -j ACCEPT
* 学会写简单的脚本