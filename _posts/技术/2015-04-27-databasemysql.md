---
layout: post
title: 实战之数据库实例操作说明(mysql)
category: 技术
tags: Note
keywords:
description:
---


####数据库的主从复制以及读写分离：
---

* 个人理解为: 主从复制，从数据库即使同步主数据库进行备份，提升安全性能；读写分离，写数据库处理事务性查询从主数据库，读数据库查询性内容从从数据库读取；


* 主从复制目的：

   * 主从服务器设置的稳健性得以提升，如果主服务器发生故障，可以把本来作为备份的从服务器提升为新的主服务器。
 
   * 在主从服务器上分开处理用户的请求，读的话，可以直接读取备机数据，可获得更短的响应时间
 
   * 用从服务器做数据备份而不会占用主服务器的系统资源。 
 

---

####测试系统ubuntu：

```
	dean@Erya:~$` id mysql`

 	`uid=119(mysql) gid=128(mysql) 组=128(mysql)` 
```

####登录


```
	dean@Erya:~$ `mysql -uroot -perya`
	`Welcome to the MySQL monitor.  Commands end with ; or \g.`
	`Your MySQL connection id is 74`
	`Server version: 5.5.41-0ubuntu0.12.04.1 (Ubuntu)`
```

```
`Copyright (c) 2000, 2014, Oracle and/or its affiliates. All rights reserved.`


`Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.`

`Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.`
```


---------



####增加(INSERT) 修改(update) 删除(DELETE) 查询(SELECT)


* 创建库

```
   mysql> `create database mybase;`
   `Query OK, 1 row affected (0.13 sec)`
```

* 进入库

```
    mysql> `use mybase;`
    `Database changed`
```

* 创建表

   * 语法：
      * create table 表名(字段，字段属性)

```
	mysql>` create table users(userid int, username varchar(20),password varchar(32), primary key(userid));`
```

* 新增数据

   * 语法：
      * insert into 表名(字段名，字段名，字段名…) value(字段值，字段值，字段值..)

```   
	mysql>` insert into users(userid, username, password) value(1, 'mysql', 'mysql');`
	Query OK, 1 row affected (0.14 sec)
```

* 查询操作

   * 语法：
      * select * from 表名， *代表所有

```
    mysql> `select * from users;`
                  
	+--------+----------+----------+

	| userid | username | password |

	+--------+----------+----------+

	|      1 | mysql    | mysql    |

	+--------+----------+----------+

	1 row in set (0.04 sec)
```

```
	mysql> `select userid from users;`
 
	+--------+

	| userid |

	+--------+

	|      1 |

	+--------+

	1 row in set (0.00 sec)
```

* 修改数据

    * 更新过程中, 除非有需求, 最好都要带上where条件, 特别是运行中的项目, 还有就是, 修改删除数据的时候, 最好能够备份一下数据库, 在重要的时候；

    * 语法:
        * update 表名 set 字段=字段值 where 字段=字段值

```   
	mysql> `update users set password = 'password' where userid = 1;` Query OK, 1 row affected (0.09 sec) Rows matched: 1  Changed: 1  Warnings: 0
  
    mysql>` select * from users;`
                  
	+--------+----------+----------+

	| userid | username | password |

	+--------+----------+----------+

	|      1 | mysql    | password |

	+--------+----------+----------+

	1 row in set (0.00 sec)
```

* 删除数据

    * 在删除数据的时候, 最好能够带上where条件, 即使是没有条件, 这样养成一个好习惯. 对以后写代码有很大的好处.;

    * 语法：
        * mysql> `delete from users where userid = 1;`
		Query OK, 1 row affected (0.05 sec)

```
    mysql> `select * from users;`
	Empty set (0.00 sec)
```

查看数据库列表：

```
mysql> `show databases;`

+--------------------+

| Database           |

+--------------------+

| information_schema |

| mybase             |

| mysql              |

| performance_schema |

| radius             |

+--------------------+

5 rows in set (0.00 sec)
```


---



####查询库中的数据表：

```
mysql> `use mybase`

Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A


Database changed


mysql>` show tables;`

+------------------+

| Tables_in_mybase |

+------------------+

| users            |

+------------------+

1 row in set (0.00 sec)
```


####查看表结构：

```
mysql> `describe users;`

+----------+-------------+------+-----+---------+-------+

| Field    | Type        | Null | Key | Default | Extra |

+----------+-------------+------+-----+---------+-------+

| userid   | int(11)     | NO   | PRI | 0       |       |

| username | varchar(20) | YES  |     | NULL    |       |

| password | varchar(32) | YES  |     | NULL    |       |

+----------+-------------+------+-----+---------+-------+

3 rows in set (0.00 sec)
```


####建库与删库

* create database 库名；
* drop database 库名；


#### 建表与删表
* create table 表名(字段，字段属性)；
* drop table 表名；


#### 查看与清空表中记录

```
mysql>` select * from users;`

+--------+----------+----------+

| userid | username | password |

+--------+----------+----------+

|      1 | mysql    | mysql    |

+--------+----------+----------+

1 row in set (0.01 sec)


mysql>` delete from users;`

Query OK, 1 row affected (0.06 sec)


mysql>` select * from users;`
Empty set (0.00 sec)
```


#### 导出数据

* 把数据库mybase导出到文件shang 中：
     * dean@Erya:~$` mysqldump -uroot -perya --databases mybase > shang`



#### 导入数据

 * mysqlimport -u root -p123456 < mysql.dbname;


---

远程连接数据库：
---

* dean@Erya:~$ `mysql -h192.168.30.193 -uroot -perya`
ERROR 2003 (HY000): Can't connect to MySQL server on '192.168.30.193' (111)


* 不能成功的原因呢可能有三：
   
     * 防火墙；
     * my.conf中skip-networking这个参数导致所有的TCP/IP端口没有监听，除了本机以外其他机器都不能连接；
     * my.conf中bindaddress，有的是bind-address  ，这个参数是指定哪些ip地址被配置，使得mysql服务器只回应哪些ip地址的请求，所以需要把这个参数注释掉;


* 此处错误为第2个，修改去掉bind-address参数；

* 重启服务：

  * dean@Erya:~$ `sudo service mysql restart`
mysql stop/waiting
mysql start/running, process 18809


---


####增加新用户

* user1为新增的用户

* 格式：grant 权限 on 数据库.* to 用户名@登录主机 identified by "密码"

* 如，增加一个用户user1密码为password1，让其可以在本机上登录， 并对所有数据库有查询、插入、修改、删除的权限。首先用以root用户连入mysql，然后键入以下命令：

`grant select,insert,update,delete on *.* to user1@localhost Identified by "password1";`

* 如果希望该用户能够在任何机器上登陆mysql，则将localhost改为"%"。
* 如果你不想user1有密码，可以再打一个命令将密码去掉。`grant select,insert,update,delete on mydb.* to user1@localhost identified by "";`



######dean@Erya:~$` mysql -h192.168.30.193 -uroot -perya`
ERROR 1045 (28000): Access denied for user 'root'@'Erya.lan' (using password: YES)

* 这个错误不知道什么原因。。。。。


######dean@Erya:~$` mysql -h192.168.30.193 -uuser1 -ppassword1`
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 38
Server version: 5.5.41-0ubuntu0.12.04.1 (Ubuntu)


Copyright (c) 2000, 2014, Oracle and/or its affiliates. All rights reserved.


Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.


Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.


mysql>
远程登录成功；



---


####查看数据库所有用户


 `SELECT DISTINCT CONCAT('User: ''',user,'''@''',host,''';') AS query FROM mysql.user;`


---
