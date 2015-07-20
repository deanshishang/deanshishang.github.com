---
layout: post
title: 命令解析之ab
category: 技术
tags: Tig
keywords:
description:
---

ab命令是apache附带的压力测试工具，可模拟各种条件对web服务器发起测试请求，可以在本地web服务器直接测试这样就除去了数据的网络传输时间以及用户pc本地的计算时间


DeantekiMacBook-Pro:~ Dean$ ab -c 20 -n 1000 www.baidu.com/

This is ApacheBench, Version 2.3 <$Revision: 655654 $>

Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/

Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking www.baidu.com (be patient)

Completed 100 requests

Completed 200 requests

Completed 300 requests

Completed 400 requests

Completed 500 requests

Completed 600 requests

Completed 700 requests

Completed 800 requests

Completed 900 requests

Completed 1000 requests

Finished 1000 requests


Server Software:        BWS/1.1          --> 被测试服务器的名称

Server Hostname:        www.baidu.com    --> 请求的url中主机的名称 www.baidu.com/news 主机就是www.baidu.com

Server Port:            80               --> web服务器监听端口


Document Path:          /                --> url的根绝对路径，例如网站http://news.ifeng.com/a/20141125/42568674_0.shtml 根绝对路径Document Path:/a/20141125/42568674_0.shtml

Document Length:        84929 bytes      --> http相应数据的正文长度

Concurrency Level:      20               --> 并发数 可设置

Time taken for tests:   100.369 seconds  --> 响应所有请求总数花费的总时间

Complete requests:      1000             --> 总请求数 可设置 

Failed requests:        981              --> 失败的总请求数字，原因在连接服务器，发送数据，接收数据等环节，以及响应超时的情况

   (Connect: 0, Receive: 0, Length: 981, Exceptions: 0)

   Write errors:           0

   Total transferred:      75821047 bytes       --> 所有请求的响应数据长度总和，包括http响应的头信息和正文数据的长度

   HTML transferred:       74937983 bytes       --> 所有响应数据的正文长度总和

   Requests per second:    9.96 [#/sec] (mean)  --> 服务器吞吐量，重点关注！！！

   Time per request:       2007.381 [ms] (mean) --> 用户平均请求等待时间

   Time per request:       100.369 [ms] (mean, across all concurrent requests) --> 服务器平均请求处理时间

   Transfer rate:          737.72 [Kbytes/sec] received --> 单位时间内从服务器获取的数据长度



Connection Times (ms)

	min  mean[+/-sd] median   max

	Connect:        1    3   3.5      2      32

	Processing:   442 1971 1180.3   1817   16698

	Waiting:        0  456 889.7    215   15195

	Total:        450 1974 1180.5   1820   16703



Percentage of the requests served within a certain time (ms) --> 描述每个请求处理时间的分布情况

	50%   1820

	66%   2573

	75%   2600

	80%   2608

	90%   2692

	95%   3603

	98%   4629

	99%   5632

	100%  16703 (longest request)



* -n 1000 表示总的请求数为1000，本次测试总共要访问页面的次数；

* -c 20 表示并发用户数为10，即一次产生请求的个数；

* url 访问的页面


