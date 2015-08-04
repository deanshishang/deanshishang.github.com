---
layout: post
title: Shell,正则表达式,expect的使用
category: 技术
tags: Note
keywords:
description:
---



* 如果人的身体是计算机，那么大脑就是内核，手或者五官就是shell，shell与计算机硬件之间是内核，用户面对的不是硬件也不是内核，而是shell，shell命令通知内核，内核去支配硬件工作


* 系统默认安装的shell(linux发行版 redhat/centos等) 是bash，是sh的升级版本

---


bash的特点：
---

* 记录历史命令  

```
history 查看历史命令
!! 查看上一条命令
```

* 指令与文件名补全(tab)

* 别名 : 可以自定义 格式为 

```
alias 命令别名='具体命令'
```

```
dean@Erya:~/SHELL$ alias
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto' 
```

* 通配符
    
     * *号匹配零个或者多个字符  
     *  ？号匹配一个字符


* 输入输出重定向 输入重定向用于改变命令的输入，输出重定向表示改变命令的输出

```
localhost:shell Dean$ echo 'abc' > file1
localhost:shell Dean$ cat file1
abc
```

```
输入重定向实例

\#!/bin/sh
while read line
do
    echo $line
done < file1
```


* 管道符 前面命令运行的结果丢给后边, 当运行一个进程时，你可以使它暂停（按Ctrl+z），然后使用fg命令恢复它，利用bg命令使他到后台运行，你也可以使它终止（按Ctrl+c）


* 作业控制 当运行一个进程时，你可以使它暂停（按Ctrl+z），然后使用fg命令恢复它，利用bg命令使他到后台运行，你也可以使它终止（按Ctrl+c）。


---

变量
---


* 环境变量
SHELL预设的一个变量

```
dean@Erya:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/home/dean/openwrt/old9341open/trunk/staging_dir/toolchain-mipsel_24kec+dsp_gcc-4.8-linaro_uClibc-0.9.33.2/bin/:/home/dean/Work/openwrt/trunk/staging_dir/toolchain-mips_34kc_gcc-4.8-linaro_uClibc-0.9.33.2/bin/
```
```
dean@Erya:~$ env
TERM=xterm-256color
SHELL=/bin/bash
XDG_SESSION_COOKIE=a926edb91db2f7a5bc0c24d20000000c-1430195108.710231-903198738
SSH_CLIENT=192.168.30.158 53968 22
SSH_TTY=/dev/pts/6
USER=dean
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arj=01;31:*.taz=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lz=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.axv=01;35:*.anx=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.axa=00;36:*.oga=00;36:*.spx=00;36:*.xspf=00;36:
MAIL=/var/mail/dean
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/home/dean/openwrt/old9341open/trunk/staging_dir/toolchain-mipsel_24kec+dsp_gcc-4.8-linaro_uClibc-0.9.33.2/bin/:/home/dean/Work/openwrt/trunk/staging_dir/toolchain-mips_34kc_gcc-4.8-linaro_uClibc-0.9.33.2/bin/
PWD=/home/dean
LANG=zh_CN.UTF-8
SHLVL=1
HOME=/home/dean
LANGUAGE=zh_CN:zh
LOGNAME=dean
SSH_CONNECTION=192.168.30.158 53968 192.168.30.193 22
LESSOPEN=| /usr/bin/lesspipe %s
LESSCLOSE=/usr/bin/lesspipe %s %s
_=/usr/bin/env
```


*  要想系统内所有用户登录后都能使用该变量

      * 需要在/etc/profile文件最末行加入 “export myname=Aming” 然后运行”source /etc/profile”就可以生效了。此时你再运行bash命令或者直接su - test账户看看。


* 只想让当前用户使用该变量

     * 需要在用户主目录下的.bashrc文件最后一行加入“export myname=Aming” 然后运行”source .bashrc”就可以生效了。这时候再登录test账户，myname变量则不会生效了。上面用的source命令的作用是，讲目前设定的配置刷新，即不用注销再登录也能生效。


* 单引号和双引号的区别：用双引号时不会取消掉里面出现的特殊字符的本身作用（这里的$），而使用单引号则里面的特殊字符全部失去它本身的作用。

* pstree这个指令会把linux系统中所有进程通过树形结构打印出来。

```
dean@Erya:~$ pstree |grep bash
     |-gnome-terminal-+-2*[bash]
     |-sshd-+-sshd---sshd---bash
     |      `-sshd---sshd---bash-+-grep
```


* export其实就是声明一下这个变量的意思，让该shell的子shell也知道变量abc的值是123.如果export后面不加任何变量名，则它会声明所有的变量。


* 系统环境变量与用户个人环境变量的文件与位置：

    * /etc/profile ：这个文件预设了几个重要的变量，例如PATH, USER, LOGNAME, MAIL, INPUTRC, HOSTNAME, HISTSIZE, umas等等。
    
    * /etc/bashrc ：这个文件主要预设umask以及PS1。这个PS1就是我们在敲命令时，前面那串字符了

```
dean@Erya:~$ echo  $PS1

\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\u@\h:\w\$   
\u就是用户，\h 主机名， \W 则是当前目录，\$就是那个’#’了，如果是普通用户则显示为’$’

```

* 除了两个系统级别的配置文件外，每个用户的主目录下还有几个这样的隐藏文件：
    * .bash_profile ：定义了用户的个人化路径与环境变量的文件名称。每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次。
    * .bashrc ：该文件包含专用于你的shell的bash信息,当登录时以及每次打开新的shell时,该该文件被读取。例如你可以将用户自定义的alias或者自定义变量写到这个文件中。
    * .bash_history ：记录命令历史用的。
    * .bash_logout ：当退出shell时，会执行该文件。可以把一些清理的工作放到这个文件中。


---


linux中的特殊命令与符号
---


*  *零个或者多个字符

*  ?
一个字符

* \# 注释

* \
转义字符 \* 还原成普通字符


* |
管道符


* $ 变量前边的标识符

```
dean@Erya:~/SHELL$ ls x.txt test.txt
test.txt  x.txt
dean@Erya:~/SHELL$ ls !$     !$表示上条命令中 最后一个执行的，此中为 ls test.txt
ls test.txt
test.txt
```

* ; 在一行中运行多个命令用;隔开就行了


* &  后台执行命令，调回前台用fg

```
dean@Erya:~$ sleep 100 &

[1] 21494

dean@Erya:~$ fg

sleep 100



dean@Erya:~$ fg

sleep 100

^Z

[1]+  已停止               sleep 100

dean@Erya:~$ bg

[1]+ sleep 100 &

Ctrl+z是暂停，然后bg是恢复执行


dean@Erya:~$ sleep 100 &

[1] 21496

dean@Erya:~$ jobs    查看后台运行任务

[1]+  运行中               sleep 100 &

dean@Erya:~$ fg 1  根据任务号恢复前台执行

sleep 100
```



* \> >> 2> 2>>

     * \>取代;
	 * >>追加; 
	 * 2>错误重定向错误信息输出路径;
	 * 2>>错误追加重定向;
	 * 2>&1 标准信息输出路径指定为错误输出路径,也就是输出都在一起; 


* []  中间为字符，表示任意一个其中的字符

* &&与|| 与或

* grep
过滤一个或者多个字符

* cut

```
dean@Erya:~/SHELL$ head -n5 passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync

dean@Erya:~/SHELL$ head -n5 passwd |cut -d ':' -f1       -d 指明分隔符  -f指明第几段
root
daemon
bin
sys
sync

dean@Erya:~/SHELL$ head -n5 passwd |awk -F':' '{print $1}'       -F指定分隔符  print $1打印第一段
root
daemon
bin
sys
sync

dean@Erya:~/SHELL$ head -n5 passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync

dean@Erya:~/SHELL$ head -n5 passwd |cut -c1-5       打印第一到第五列
root:
daemo
bin:x
sys:x
sync:

dean@Erya:~/SHELL$ head -n5 passwd |cut -c5     截取第几个字符
:
o
x
x
:
```

* sort

dean@Erya:~/SHELL$ cat c.txt

d

d

g

c

a

b

c

d

e

f

g

h

g

f

dean@Erya:~/SHELL$ cat c.txt |sort

a

b

c

c

d

d

d

e

f

f

g

g

g


h

dean@Erya:~/SHELL$ cat c.txt |sort -u   去重复

a


b

c

d

e

f

g

h

dean@Erya:~/SHELL$ cat c.txt |sort -u -r  反序

h

g

e

d

c

b

a


dean@Erya:~/SHELL$ cat n.txt

1

2

3

10

3

2

34

dean@Erya:~/SHELL$ cat n.txt |sort -u         

1

10

2

3

34

dean@Erya:~/SHELL$ cat n.txt |sort -u -n  以数值排序

1

2

3

10

34




* wc
    * 统计行数-l
    * 统计字符数-m 
    * 统计词数-w


* uniq

    * 取唯一，配合sort用
    
dean@Erya:~/SHELL$ cat x.txt

ashag

1


1

1

1

1


1

a

abc/shi abc abc

ab

23

111

2o3

rotshishangtet:bash


dean@Erya:~/SHELL$ sort x.txt |uniq -c

1

6 1

1 111

1 23

1 2o3

1 a
 
1 ab

1 abc/shi abc abc

1 ashag

1 rotshishangtet:bash


6,tee



dean@Erya:~/SHELL$ cat x.txt

ashag

1

1

1

1

1

1

a


abc/shi abc abc

ab


23

111

2o3

rotshishangtet:bash


* tr

     * 替换字符，常用来处理文档中出现的特殊符号，常用是转换大小写 tr 'a-z' 'A-Z'



* split


dean@Erya:~/SHELL$ split -b 500 passwd passwd    -b 依据大小切割文档

dean@Erya:~/SHELL$ ls passwd*

passwd  passwdaa  passwdab  passwdac  passwdad

dean@Erya:~/SHELL$ cat passwdab |wc -m

500

dean@Erya:~/SHELL$ cat passwdad |wc -m

464



dean@Erya:~/SHELL$ split -l 10 passwd passwd    -l 依据行数来切割文档

dean@Erya:~/SHELL$ cat passwdaa |wc -l

10


---

rc.d init.d 软链接
---


dean@Erya:~$ ls -ld /etc/rc0.d/*

lrwxrwxrwx 1 root root  17  4月  2 22:51 /etc/rc0.d/K09apache2 -> ../init.d/apache2

lrwxrwxrwx 1 root root  29  7月 15  2014 /etc/rc0.d/K10unattended-upgrades -> ../init.d/unattended-upgrades





dean@Erya:~$ ls -ld /etc/init.d/*

lrwxrwxrwx 1 root root   21  7月 15  2014 /etc/init.d/acpid -> /lib/init/upstart-job

lrwxrwxrwx 1 root root   21  7月 15  2014 /etc/init.d/anacron -> /lib/init/upstart-job

-rwxr-xr-x 1 root root 7621  2月  7  2012 /etc/init.d/apache2



* rc*.d都是指向/etc/init.d/ 下的软链接 




* shell脚本不需要编译跑在shell中，是一些命令的集合


* sh -x test.sh 

     * -x 参数支持查看执行过程


* 将test.sh加上可执行权限 即可./test.sh执行脚本


dean@Erya:~/SHELL$ chmod u+x test.sh

dean@Erya:~/SHELL$ ls -al test.sh

-rwxrw-r-- 1 dean dean 39  4月 24 10:48 test.sh

dean@Erya:~/SHELL$ ./test.sh

2015年 04月 24日 星期五 11:46:11 CST

Hello World.


d=`date +%H:%M:%S`  d后紧跟=符号

---


使用expect实现shell自动交互(自动登录脚本)  
---


* \#!/usr/bin/expect -f                                              --------告诉操作系统使用哪一个shell来执行


set server 192.168.30.193 

set pass [lindex $argv 0]     -------- argv 0 第一个参数

set username dean

set timeout 10                                                     -------- 设置时延

spawn ssh $username@$server                        -------- 启动一个新的进程 spawn

expect {

"*yes/no" { send "yes/r"; exp_continue}          -------- send 执行交互动作，expect判断上次输出结果是否包含 yes/no

"*password" { send "$pass\r"}

}


expect "*Last login*" interact                               --------- 执行完成后保持交互状态，把控制权交给控制台，这个时候就可以手工操作了。如果没有这一句登录完成后会退出，而不是留在远程终端上。



* 如果是登录过去执行命令则在最后添加　　

expect elf 

exit

---

SHELL脚本中
---



sh test.sh     1    2

     |         |     |
       
     $0        $1 $2


dean@Erya:~/SHELL$ cat 1.sh
 
 \#!/bin/bash

echo "\$! is $!"

echo "\$# is $#"

echo "\$* is $*"

echo "\$@ is $@"

echo "\$$ is $$"

echo "\$? is $?"

echo "\$0 is $0"

echo "\$1 is $1"

echo "\$2 is $2"


dean@Erya:~/SHELL$ sh 1.sh 1 2 3 4 5 6 shi shang "test 3"

$! is                                                           ----------Shell最后运行的process的PID   ???

$# is 9                                                       ----------参数个数

$* is 1 2 3 4 5 6 shi shang test 3              ----------- $* 所有参数列表 "1 2 3 4 5 6 shi shang "test 3"

$@ is 1 2 3 4 5 6 shi shang test 3            ----------- $@ 所有参数列表 "1" "2" "3" "4" "5" "6"  "shi" "shang" "test 3"

$$ is 15409                                               ----------Shell本身的PID

$? is 0                                                       ---------- 最后运行的命令结束代码

$0 is 1.sh                                                  ----------  脚本名字本身

$1 is 1

$2 is 2




* shift 参数
     * 位置参数可以用shift命令左移。比如shift 3表示原来的$4现在变成$1，原来的$5现在变成$2等等，原来的$1、$2、$3丢弃，$0不移动。不带参数的shift命令相当于shift 1。





[ -lt 1 ]    小于

[ -le 1 ]   小于等于

[ -ge 1 ]  大于等于

[ -gt 1 ]   大于


[ -eq 1 ]   等于

[ -ne 1 ]   不等于


-e filename 文件或者目录是否存在

-d /home 判断是不是目录并是否存在

-f filename 判断是不是普通文件并是否存在

-r w x 判断是否有读写执行权限


read  读


read -p "please input a number: " x  类似于echo 但是可以读入x;



#####逻辑判断：


if XXX ; then

     XXX
     
elif XXX; then

     XXX
     
else

     XXX
     
fi


case $n in

    1)
    
        echo "the number is odd"
        
        ;;
        
    0)
    
        echo "the number is even"
        
        ;;
        
esac




#####循环逻辑


for arg in $X; do

     XXX
     
done


while []; do

     XXX
     
done



另外你可以把循环条件忽略掉，笔者常常这样写监控脚本。

while :; do

command

done


* echo -n "Your choice is " -n 不断行在同一行显示


#####函数：


function sum() {

     echo "xxxx"
     
}


* seq \`1 100\`  1到100序列



---





正则表达式
---


####awk

* 截取某个段：

cat test.txt

192.168.11.11

head -n1 test.txt | awk -F'.' '{print $1}'

192

head -n2 test.txt |awk -F'.' '{print $2"@"$3}'     print可以打印自定义的内容，需要用双引号括起来，比如实例中的"@"




* 匹配字符或者字符串

awk '/shishang521/' test.txt   匹配test.txt中含有shishang521的字符串


awk -F'.' '$1~/192/' test.txt 按段匹配，分隔符为'.'第一段中匹配192的字符串，注意~的添加


dean@Erya:~/SHELL$ awk -F'.' '/192/ {print $3}' test.txt

111

0

0

1

1


awk -F'.' '/192/ {print $3} /0/' test.txt

111

0

192.168.0.222

0

192.168.0.222

1

1




* 条件操作符

     * awk中是可以用逻辑符号判断的，比如’==’就是等于，也可以理解为“精确匹配”。另外也有’>’, ‘>=’, ‘<’, ‘<=’, ‘!=’ 等等，值得注意的是，即使$3为数字，awk也不会把它当数字看待，它会认为是一个字符。所以不要妄图去拿$3当数字去和数字做比较。



dean@Erya:~/SHELL$ awk -F'.' '$3=="0"' test.txt

192.168.0.222

192.168.0.222

dean@Erya:~/SHELL$ awk -F'.' '$3=="111"' test.txt

192.168.111.1

192.168.111.1

dean@Erya:~/SHELL$ cat test.txt | awk -F'.' '$2!=""'      $2!=""  空

192.168.111.1

192.168.0.222

192.168.0.222

192.168.1.111


dean@Erya:~/SHELL$ cat test.txt | awk -F'.' '$2!="" && $3!="111"'    逻辑比较

192.168.0.222

192.168.0.222

192.168.1.111

192.168.1.123




dean@Erya:~/SHELL$ awk -F'.' '/192.168.111/ {print NF}' test.txt    NF变量表示被分隔符分开后一共多少段

4

4

4

4

4



dean@Erya:~/SHELL$ awk -F'.' 'NR<=4 && $3~/111/' test.txt       NR表示行数

192.168.111.1

dean@Erya:~/SHELL$ awk -F'.' 'NR<=4 && $3~/0/' test.txt

192.168.0.222

192.168.0.222


* 数学运算

dean@Erya:~/SHELL$ awk -F'.' 'NR<4 && $1="154"' test.txt

154 168 111 1

154 168 0 222

154 168 0 222



dean@Erya:~/SHELL$ head -n4 test.txt | awk -F'.' '{$3=$2-$1; print $1, $2, $3}'

192 168 -24

192 168 -24

192 168 -24

192 168 -24



dean@Erya:~/SHELL$ head -n4 test.txt

192.168.111.1

192.168.0.222

192.168.0.222

192.168.1.111

dean@Erya:~/SHELL$ head -n4 test.txt | awk -F'.' '{{tot+=$3}};END {print tot}'     注意括号的使用

112

这里的END要注意一下，表示所有的行都已经执行，这是awk特有的语法，其实awk连同sed都可以写成一个脚本文件，而且有他们特有的语法，在awk中使用if判断、for循环都是可以的，只是笔者认为日常管理工作中没有必要使用那么复杂的语句而已。


* 测试

     * 打印文档全部内容  awk '{print $0}' test.txt   $0整行
     
     * 用’:’作为分隔符，查找第一段为’root’的行，并把该段的’root’换成’toor’(可以连同sed一起使用)；awk -F':' '$1~/root/' test.txt | sed 's/root/toor/' 
     
     * 用’:’作为分隔符，打印最后一段；awk -F':' '{print $NF}' test.txt

     * 用’:’作为分隔符，打印第一段以及最后一段，并且中间用’@’连接 （例如，第一行应该是这样的形式 “root@/bin/bash”；awk -F':' '{print $1"@"$NF}' test.txt

     * 用’:’作为分隔符，把整个文档的第四段相加，求和；awk -F':' '{(sum+=$4)};END {print sum}' test.txt


####grep


 * 实现查找功能 但是并不能修改或者替换  行！


 * tr [a-z] [A-Z]  转换小写为大写


-c  匹配行的数目

-i   忽略大小写 

-v  打印出不符合大小写的行

-n  打印出内容所在的行数

-A  后边接数字 打印符合内容的行以及下面几行

-B  同-A 上面几行

-C  同-B 上面几行与下面几行


dean@Erya:~/SHELL$ cat x.txt

1

a

abc

ab

23

111

23

345

dean@Erya:~/SHELL$ cat test.txt |grep '^[0-9]' x.txt

1

23

111

23

345

dean@Erya:~/SHELL$ cat test.txt |grep '[^0-9]' x.txt

a

abc

ab


dean@Erya:~/SHELL$ cat test.txt |grep '[23]' x.txt

23

23

345


dean@Erya:~/SHELL$ cat test.txt |grep '[13]' x.txt     只含有1或者3  不是13

1

23

111

23

345



* 如果要过滤出数字以及大小写字母则要这样写[0-9a-zA-Z]。另外[ ]还有一种形式，就是[^字符] 表示除[ ]内的字符之外的字符。



dean@Erya:~/SHELL$ cat x.txt

1


a

abc

ab

23

111

23

345

dean@Erya:~/SHELL$ cat test.txt |grep '^1' x.txt


111

dean@Erya:~/SHELL$ cat test.txt |grep '1$' x.txt

1

111


* 在正则表达式中，”^”表示行的开始，”$”表示行的结尾，那么空行则表示”^$”,如果你只想筛选出非空行，则可以使用 “grep -v ‘^$’ filename”得到你想要的结果




dean@Erya:~/SHELL$ cat test.txt |grep '^[^a-zA-Z]' x.txt    不以大小写字母开头的行

1

23

111

23

345


* “.”表示任意一个字符


* “*”表示零个或多个前面的字符



* ‘.*’表示零个或多个任意字符，空行也包含在内





dean@Erya:~/SHELL$ cat test.txt |egrep '1{2}' x.txt

111

dean@Erya:~/SHELL$ cat test.txt |grep '1\{2\}' x.txt

111

dean@Erya:~/SHELL$ cat test.txt |egrep '1\{2\}' x.txt

dean@Erya:~/SHELL$ cat test.txt |grep '1{2}' x.txt


* 这里用到了{ }，其内部为数字，表示前面的字符要重复的次数。上例中表示包含有两个o 即’oo’的行。注意，{ }左右都需要加上脱意字符’\’。另外，使用{ }我们还可以表示一个范围的，具体格式是 ‘\{n1,n2\}’其中n1<n2，表示重复n1到n2次前面的字符，n2还可以为空，则表示大于等于n1次。





dean@Erya:~/SHELL$ cat test.txt |grep '2*' x.txt      0个或者0个以上前边的字符

1

a

abc

ab

23

111

2o3

234

23

345

dean@Erya:~/SHELL$ cat test.txt |egrep '2+' x.txt    1个或者1个以上前边的字符       egrep == grep -E

23

2o3

234

23



dean@Erya:~/SHELL$ cat test.txt |egrep '2?' x.txt    0个或者1个以上前边的字符

1

a

abc

ab

23

111

2o3

234

23

345



dean@Erya:~/SHELL$ cat test.txt |grep -E 'o|5' x.txt     或 |

2o3

345

dean@Erya:~/SHELL$ cat test.txt |grep -E 'o|5|3' x.txt

23

2o3

234

23

345

dean@Erya:~/SHELL$ cat test.txt |grep -E '(11)+' x.txt   ()表示一个整体  此中为 1个或者1个以上(11)

111


####sed


* sed或者awk都是流式编辑器，可以替换或者查找编辑等




* 打印


dean@Erya:~/SHELL$ cat x.txt

1

a

abc

ab

23

111

2o3

234

23

345

dean@Erya:~/SHELL$ sed -n '3'p x.txt           -n 打印第几行  '3'p 第三行

abc

dean@Erya:~/SHELL$ sed -n '2'p x.txt

a




dean@Erya:~/SHELL$ sed -n '2,$'p x.txt    打印第几行到第几行 

a  

abc

ab

23

111

2o3

234

23

345

dean@Erya:~/SHELL$ sed -n '2,5'p x.txt

a

abc

ab

23


dean@Erya:~/SHELL$ sed -n '/abc/'p x.txt   打印匹配的行

abc

dean@Erya:~/SHELL$ sed -n '/ab/'p x.txt

abc

ab




dean@Erya:~/SHELL$ sed -n '/^ab/'p x.txt   ^ $同样适用

abc

ab

dean@Erya:~/SHELL$ sed -n '/^a/'p x.txt

a

abc

ab

dean@Erya:~/SHELL$ sed -n '/^1/'p x.txt

1

111

dean@Erya:~/SHELL$ sed -n '/c$/'p x.txt

abc



dean@Erya:~/SHELL$ sed -e '3'p -n x.txt

abc

dean@Erya:~/SHELL$ sed -e '3'p -e '/23/'p -n x.txt

abc

23

234

23




* 删除

dean@Erya:~/SHELL$ sed '1'd x.txt

a

abc

ab

23

111

2o3

234

23

345

dean@Erya:~/SHELL$ sed '1,3'd x.txt   删除1到3行

ab

23

111

2o3

234

23

345





* 替换字符或者字符串


dean@Erya:~/SHELL$ cat x.txt

1

a

abc abc abc

ab

23

111

2o3

dean@Erya:~/SHELL$ sed 's/ab/ba/' x.txt   替换匹配ab的行的第一个ab为ba

1

a

bac abc abc

ba

23

111

2o3

dean@Erya:~/SHELL$ sed 's/ab/ba/g' x.txt  加上了g表示 匹配行中ab全部替换为ba

1

a

bac bac bac

ba

23

111

2o3





dean@Erya:~/SHELL$ sed 's/[0-9]//g' x.txt   删除所有数字

a

abc abc abc

ab


o



dean@Erya:~/SHELL$ sed 's/^\(rot\)\(.*\)\(bash\)/\3\2\1/' x.txt     替换rot与bash的位置 \3\2\1    修改了对应位置

1

a

abc abc abc

ab

23

111

2o3

bashshishangtet:rot

dean@Erya:~/SHELL$ sed 's/^.*/123&/' x.txt     开头加上123

1231

123a

123abc abc abc

123ab

12323

123111

1232o3

123rotshishangtet:bash

dean@Erya:~/SHELL$ sed 's/^.*/&123/' x.txt  结尾加上123

1123

a123

abc abc abc 123

ab123

23123

111123

2o3123

rotshishangtet:bash123


* 如果想直接修改文件，那么直接sed 加上-i参数就可以了


dean@Erya:~/SHELL$ sed 's/abc/bac/g' x.txt

1

a

bac/shi bac bac

ab

23

111

2o3

rotshishangtet:bash





dean@Erya:~/SHELL$ sed 's#abc/shi#bac/ihs#g' x.txt   注意如果替换的当中有/ 那么直接用#号隔开就可以

1

a

bac/ihs abc abc

ab

23

111

2o3


* 例子

     * 把test.txt中第一个单词和最后一个单词调换位置；sed 's/\(^[a-zA-Z][a-zA-Z]*\)\([^a-zA-Z].*\)\([^a-zA-Z]\)\([a-zA-Z][a-zA-Z]*$\)/\4\2\3\1/' test.txt

     * 把test.txt中出现的第一个数字和最后一个单词替换位置；sed 's#\([^0-9][^0-9]*\)\([0-9][0-9]*\)\([^0-9].*\)\([^a-zA-Z]\)\([a-zA-Z][a-zA-Z]*$\)#\1\5\3\4\2#' test.txt

     * 把test.txt 中第一个数字移动到行末尾；sed 's#\([^0-9][^0-9]*\)\([0-9][0-9]*\)\([^0-9].*$\)#\1\3\2#' test.txt



