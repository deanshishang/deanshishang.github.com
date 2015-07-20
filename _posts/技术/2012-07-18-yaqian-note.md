---
layout: post
title: 嵌入式C语言编程
category: 技术
tags: Lang
keywords:
description:
---

###表达式

#### 数据类型

* 内置类型：int short chart double ;

* 构造类型：结构体 指针 数组 枚举 函数 ;

* 空类型：void ;

* int   short long                     三种整型;

   |      |     |

    4byte 2byte (32bit 4byte；64bit 4byte）

#### tag 知识点

* CPU 指令个数 寄存器指令个数 ————> 体系结构；
    
* intel amd --> i386 体系结构 地址总线：32位 访问的地址总量：2*2...(32),4G.

* 在32位上8个字节用long long（long double 16字节）

* c89中用long long;

* gcc a.c -o app -sd=c89;

* 运算符：size of()字节数求取；

* typeof()求变量类型的名字；

* int-->32bits ;

* 在内存中两种稳态：二进制表示 ;

* 编码方式：补码 ;

* 0有唯一编码，计算速度快;

* 32位 第一位;符号位（1负0正）；

* 范围-2（8n-1）~~2(8n-1)-1;n为字节数，量级int 10(9),short 10(5);

* 如果超出范围就溢出了（overfloat）;

* 浮点数：float型，作为协处理器集成于cpu中；用移码表示，常用于科学图形计算当中，有符号位，后面31位中前M位为数值，后N位为精度；

* 浮点计算速度慢于整数;

* 中央处理器『协处理器，中央处理单元 内部管理单元（memory management unit）(虚拟地址 物理地址)』之所以在主板上消失了是因为都集成于CPU中了。计算机中处理整数速度快;

* char 字符型数据,占用一个字节，存储字符对应的ASCII码；

* 除法永远向下去整;

* ALU(agrithal logical uint)算数逻辑单元 只做加法和移位运算;

* 编程中注意速度避免用除法 乘法可以;

* 逻辑运算符 支持短路运算（short circuit）;

     |

  char *who;

  (strcmp(who,"root")!=0)&&(printf("you are not root"));

  等同于 if(strcmp(who,"root")!=0){printf("you are not root")};

* 隐式类型转换-->自动统一到下一个类型进行运算 小类型转换成大类型 保持精度;
 
  |

    char-->short-->int -->unsigned int -->long-->double<--float;

### 数组与字符串

#### 数组

变长数组 malloc int a[n];与编译器相关其采用默认的N值进行分配 不显示错误；

例子：int a[10];a[10]不行，结果不可预测，造成数组越界了；

* %d: 输出转换成十进制有符号整型
* %u：无符号整型
* %o，%x：用来查看 不用有符号
* %s：字符串，%c字符
* %p：专门用来打印地址，pointor

####存储模式
* 小端法（little-endian）i386体系：高位存高字节，低位存低字节。

* 在编程时用虚拟地址，不是物理地址，内存不够时可以用外存。缺页异常。

* 访问速度：缓存50ns，内存100ns，磁盘100us，移位运算1ns，/，%10ns。

* 局部性原理：数据最好连续性访问。

* 解释操作系统：分时复用。即不同程序之间相互一直切换。

* 虚拟的经过MMU进行映射（内核提供算法）物理的，disc与内存进行交换，硬盘中有用的换取内存中无用的（换页机制，即缺页异常）。

* 佐证:访问占用内存很大的时候，不断用页面切换到磁盘中暂存即磁盘与内存的交互。

#### 串

##### 字符串：特殊的字符数组；多了一个结束标记“\0”，字符串首地址开始到“\0”之间的内容。例如五个字符的串是六个字节。

##### 字符串的初始化：

* char buf[1024]="hello";前5个是hello第六个是\0，以后的也都是“\0”。char buf[5]="hello";是错误的 六个字节。

* int a[10];

* sizeof(a),求取字节数为40。

* char buf[]="hello";

* sizeof(buf)，为6.

* sizeof(&buf[0]),地址是32位，即4个字节。

* 常用函数：输入一个串 char buf[1024];

* scanf("%s",buf);\\输入一个串到buf中

* gets(buf);

* puts(buf);\\打印串

* strlen 求字符串长度；strcmp 字符串比较；strcpy 字符串复制；strcat 字符串连接；    

* scanf函数对空格非常敏感（空格与回车等价），这时只能用gets（）函数。

* gets()函数没有限制输入的字符，只传输了首地址，会导致stack smash(栈崩溃)。


### 函数与栈帧

#### 函数（子程序）

* 返回值 函数名 （参数列表）\\函数的声明的话可以只写参数的类型，包括形参和实参。

* 函数名代表底层中代表函数的入口地址（编译器，入口地址，跳转），函数的第一层指令的地址。

* objdump 反汇编（a.c-->app（二进制可执行程序）--> 汇编中的汇编语言)，把一个二进制程序反成汇编语言。

  * -d 反汇编;

  * -S 列出源（通过反汇编方法了解C）;

  * -g 为程序生成一个可供指示的符号表。app>out 就可以用vi编译器查找out中的代码;

* 汇编中没有数据类型的概念，主要有寄存器和内存。

  * 单：指令 操作数；

  * 双：指令 操作数1，操作数2；

  * 寄存器：eax，b c d:通用寄存器，四个字节的存储空间，esi,sdi:下标寄存器，数组的下标，不能存负数。esp（栈顶地址）,ebp（栈底地址）:只能存地址，即指针寄存器。eip 保存下一条指令

  * 地址。跳转都是eip相关的。

  * 栈，先进后出。

#### 指令

* push %ebp :寄存器中的值压到栈里边去，esp 指向哪里就把值存到哪里。1，将esp向下移动，2，将ebp值保存到esp指向的空间;

* pop %ebp ：出栈，1，取出栈顶的值，将esp向上移动;

* call 地址 ：跳转指令，调用函数（跳到函数入口出执行） push %eip;

* return :返回指令，取出返回地址 pop %eip 还原到eip中;

* mov：（双操作指令） （数，寄存器）（寄存器，寄存器）（（寄存器），寄存器（找对应的内存单元的值放到第二个寄存器中））；

* mov -8（%esp）,%eax（%esp的地址减8，把对应内存中的值放入eax中）；

* leave:1,mov %ebp,%esp,2,pop %esp；

#### 其他

* 返回值都是通过eax寄存器返回的。

* 参数的压栈顺序是：从右向左依次入栈。

* 栈从上向下生长，栈顶esp栈底ebp 上面是栈底，从上向下生长。

* GDB

  * gdb list+行号 刻出代码

  * break+行号 设置断点

  * print+变量名 打印出值

  * next 跳转函数

  * step 进入函数里边

  * si 分条指令进行执行

  * disassemble 反汇编当前函数 +函数名或地址 反汇编指定的

  * inforegisters 显示所有寄存器当前值

  * start 从程序命令一步一步的执行

  * quit 退出

  * echo 打印

* 函数返回值：没有RETURN 就得返回上一个程序main的返回值。

* 段错误解释：返回值是eax 但是不能超过四个字节。

* 局部变量的产生：移动esp产生值，是随机的，上一个栈帧残留的。

* 全局变量和局部变量：1，作用域上有差别；2，生命期不同，全局变量一直存在，局部变量调用后就没有了，前者一直存在，后者调用后就没有了；3，全局变量存在bss data段，局部变量存储在栈帧上，即存储的位置不同。


### C程序内存布局

#### ELF文件格式内部结构(磁盘中的布局图)

* ELF header:查看汇总信息头，保存可执行文件的执行信息

* 段   

  * .text:代码段，存储二进制字节码

  * .data：数据段， 存储全局变量（局部变量放在栈帧里）

  * rodata:只读数据段， 存储字符串常量 “ ”。

  * bss:块缓冲段，存储未初始化的全局变量，beiler save space 节省空间。

  * 用于链接  .rel.data:在模块链接数数据的重新定位。

  * .rel.text:在模块链接时代码的重新定位。

  * .symblol:文件内的名字 保存全局符号。

  * .debug:给gdb用调试信息的。

* ELF（tail）

##### objcpy 把ELF中除了四个段以外的全部内容删掉。

##### 从磁盘中拷贝到内存的过程叫做加载（load）。

##### ldd app 查看库

#####  readelf -s 读取ELF文件中各个段的表象。


### 链接

####一个源文件生成最后的可执行程序: a.c-->1 a.i-->2 a.s-->3 a.o-->4 app(二进制可编程程序)

* 预处理（比如注释，.c文件转换成有用的源代码，加快转换速度）；

* 编译（翻译成汇编语言代码）；

* 汇编 目标文件 .o;

* 链接 合并的过程，将多个.o文件合成一个可执行程序。

* -E 只执行预处理 -S 只进行编译 -c 只进行汇编

#### 其他

* 虚拟地址=段首地址（由链接脚本决定）+逻辑地址（从0 开始，即偏移）。

* 偏移不是真值，引用函数的时候需要重新填写，这个操作成为重定位。

* 没一个.c文件对应一个.o，多个.c对应多个.o，（函数的相对位置发生了变化），程序链接：函数和全局变量位置发生变化，call调用时地址可能无效，改成链接后的地址再进行重新定位。

* 多文件编译：两个或者多个.o文件合并的过程为链接。

* readelf -S app.o 读取各个表象

* objdump -xdS   -x 是列出重定位信息

* 链接与加载的信息包含INIT 函数（程序入口 call main函数），在合并.o文件过程中加进来，.text代码段增加许多。

* 符号解析规则 int a;(声明)int a=100;(定义 初始化了的a)

  * 不允许有多个定义，不允许同一个符号定义多次；（定义成什么值无所谓就是不能重新定义）；

  * 有一定义多个声明，选择使用符号时使定义的符号；（允许一个符号定义，多次声明可以）；

  * 多个声明则选择其中一个（对于一个变量多次声明，只有声明没有定义是允许的，因为其中一个自动升为定义，但函数不允许，其中必须要定义）；

* 内部局部变量和外部全局变量不冲突，因为作用域不同；函数内部引用全局变量是可以的。
{% highlight objc %}
double x;
void f()
{
	x=0;
}
#include<stdio.h>
int x = 15132;
int y = 15134;
void f();
int main(int argc,char *argv[])
{
	f();
	printf("x=0x%x y=0x%x\n",x,y);
}
{% endhighlight %}

输出结果是 0 0 ，可以用重定位的只是解释，反汇编查看可以知道地址可以回填但是指令并没有改变，所以x过了4个字节后把y的4个字节都覆盖了，都是零了，结果就是 0 0。
																							     
#### 动态库（共享库）DLL dynamic linking library

* 库中包含所有函数的实现（二进制字节码的形式存在）；二进制可执行文件（.elf）

* so中有几个ID要存储，在.elf中多了一张符号表，存在与代码段和数据段之间，指定一个ID就可以查询对应的函数入口地址。即动态库中ID与入口地址是一一对应的，.so文件拥有的。

* 标准C库中有900K。

#### 动态库的特点：

* 动态链接（发生在程序运行的时候）:编译时并不合在一起而是作标记，添加信息。运行时直接连接内存中有的库。如果库出现在内存中，直接加载应用不用加载库了，如果库不存在，则先加载库，再加载程序，即动态库的加载一定在程序加载之前。当程序运行时库和程序之间不断进行跳转。

* 共享（如果在程序运行之前库已经存在，在内存中被多个程序共享使用，节省了空间）；

#### 函数地址变化，但是对应的PLT表地址固定（puts@plt--puts 函数在 plt中的地址）

#### 运行时 GOT表，GOT【2】动态入口地址；GOT【1】动态链接器的描述信息；GOT【0】第0个表项。寻早要用的函数给地址，此过程为延迟绑定（懒惰算法），PLT表项中16个字节清空，放入函数地址，下一次用的时候直接跳转到表项中获取函数地址。

{% highlight objc %}
gcc main.c -c (汇编 生成.o文件)

gcc main.o -o app 

ldd app (产生相关库信息自动加载的)

objdump -dS app>out （反汇编）

objdump -dS -j .plt app>out     -j 只反汇编一个段 

objdump -dS -j .got.plt app>>out 追加    

{% endhighlight %}

#### printf 格式化打印；puts（）不用格式化打印；

####制作动态库: -Share 可以动态链接的 -fPIC 生成未使用过的代码，生成id首地址对应的表。

* gcc add.c -o add.so -Shared -fPIC

* readelf -dS add.so 段查询 

* 自己编写的程序就可以链接动态库

* gcc main.c ./add.so -o app -->包含动态库信息的app

* objdump -dS app

* 更新即版本不同（mv 指令实现）动态库更新方便 只要接口不变 只需要应用程序暂停再重新链接动态库使用。

#### 自主学习的能力很中要，要有基本概念，例如，现代化工业软件开发，如见开发和发布的模式接口设计模式等等。add.so（产品），add.h（放在main.c中声明）add.h 帮助文档提供给用户。.so文件也要打包给用户。

### 预处理

文件包含，宏定义，条件编译，对使用者有用但是对其自己没有用

* linux下可执行程序都是ELF格式的

* 文件包含；（机制是复制）

  * #include<stdio.h> 间括号 是系统指定的目录中搜索文件 /usr/include

  * #include"stdio.h" 双引号 当前目录下搜索需要的头文件

  * 需要注意的是：头文件中可以有：

    * 函数的声明；

	* 可以加全局变量的声明；

	* 宏定义可以有；
	
    * 新的类型定义；

	* 用include包含其他文件；不能有的是：变量的定义和函数的定义。


  * 隐式声明：要是调用一个函数之前应声明，但是gcc编译器自动判断函数类型。

  * gcc中指定文件包含的路径，a.c中head.h不再当前目录下而在include目录下，则如下命令 gcc a.c -c -Iinclude
						    
* 宏定义，让程序更简洁，代码修改更方便，易于维护，易于理解。

  * 定义一个宏，#define 标识符 字符串 #define G 9.8 

  * 注意：宏的定义不作数据检查，有效范围是限于文件内部，提前取消宏的话 # undef 宏名

  * 定义一个全局的宏：放入头文件中，每个.c include头文件就可以了

  * 带参数的宏（很像函数 但有区别），#define 宏（参数表） 字符串  

  * 宏的参数没有类型（因为是在预处理时发生的） 宏定义多行时用续行符‘\’

  * 宏的副作用 #define test(a,b) (a)*(b) 因为 test(a+b,a-b)时 可能a+b*a-b  运算顺序发生了变化。前面的是正确写法。

  * 宏与函数的区别：

    * 宏是编译时的概念，函数是运行时的概念；
	
	* 宏在编译时发生运行速度比较快，函数在运行时发生速度比较慢（压栈，跳转等）;
	
	* 宏不会对参数类型进行检查，函数有严格的类型检查；
	
	* 宏不会分配存储单元，函数自己分配存储单元（函数会产生栈帧，宏不会）；
	
	* 宏会使代码体积变大，而函数不会；
	
	* 宏不可以调用本身，函数可以。

  * inline关键字 将函数展开，不进行压栈跳转等，在过程中直接将函数展开。

  * 内联：不跳转，压栈等类似于宏，是更好的宏。把代码给展开不进行跳转，内联函数有宏的特性，又弥补了宏的特点，即不进行检查，在优化后进行展开。

  * 编译器可以自己指定内联的函数，inline 可以手动指定内联函数。

* 条件编译
{% highlight objc %}
#ifdef 标识符 程序段1（如果有标识符，则。。。）
#else 程序段2 （否则。。。）
#endif
有什么用？--> 调试输出（调试开关）只要一个地方作改动，整体都变化。调试开关是一个技巧。
gcc DEBUG.c -o app3 -g -DDEBUG //由编译器在我们的源代码中定义某个宏
#ifdef DEBUG 
#define DEBUG_PRINT1(arg0,arg1)printf(arg0,arg1);
#else
#define DEBUG_PRINT2(arg0,arg1)printf(arg0,arg1);
#endif
int _strlen(char str[])
{
	int i;
	for(i=0;str[i]!='0';i++)
		DEBUG_PRINT1("%c",str[i]);
	return i;
}
{% endhighlight %}

### Makefile

#### 基本
* 自动编译（许多.c文件）输入一个命令就可以自动编译

* 选择编译（实现筛选，自动编译）

* target(产品):prerequest(依赖软件)    要生成产品就要有很多依赖文件。

			   commond 命令（怎么样依赖文件来生成产品）

* 依赖的文件可以修改但是一定要存在；

* 命令必须以制表位开头；

* 产品 目标 命令 --> 规则，Makefile中就是一个接着一个的规则。

* 依赖文件要新于目标文件，用较新的文件生成更新的目标文件。体现了选择性编译，看依赖文件是否新于目标文件时间上能看出，就能生成新的目标。

#### 多条规则的Makefile

* 如果文件和目标文件没有关系则没有用，即孤岛规则。默认规则目标为最终目标。

* 执行Makefile的最终目的就是执行最终目标。

* make 目标名 手动指定最终目标。

* 规则中只有目标是必须的，命令与依赖可以省去，例：（release 发布程序） 
{% highlight objc %}
release：a.c
	gcc a.c -o release (strip -s release)用一个规则可以执行多个命令。
debug:a.c
	gcc a.c -o debug -g DDEBUG
clean:                    --> 命令一定被执行到
	rm -f *.o
	rm -f app
	echo "job done"
{% endhighlight %}

* clean 是伪目标，不依赖于任何文件，只要能被执行到一定执行命令，用来快速删除编译过程中产生的中间产品。

* all：app app-r all最终生成的可以没有命令。

* 快速生成依赖工具 
{% highlight objc %}
all:*.c
   gcc *.c -o app
{% endhighlight %}

### 指针与指针操作

#### 解释

* 类型名 *变量名 --> int *p = &a;

  * p 直接引用 ；引用p的值  *p 间接引用 ; 取指针变量指向的变量

  * 指针变量也是变量。

  * int *p,q;//同类型的变量可以写在一行

  * int a,b； p=&a;

  * q=&b;//整型变量不用赋&b的值，会出现警告。
								    
* typedef 已有类型 新类型（别名 _t是标准）;//把程序中已知类型定义成自己的新类型

  * typedef int my-int_t;

* typedef int * int p_t; int p_t p,q;//p,q都是指针类型，此处体现了与宏定义的不同，要是宏的话，q就不是指针类型了。

* 直接引用的不用管p中的内容新的内容直接把它覆盖了。

* 间接引用 *p=10；将10付给p所指向的空间，可能引起段错误，用间接引用的要清楚p所指向的空间是否有效，否则引起段错误。

* 数组是首地址，指针也是地址，则*p <=> p[0]

  * char *p="hello";//p指向的空间是只读数据段，*p=‘h’；//不可以，因为只读数据段不能修改，出现段错误

  * char p[]="hello"; *p='H'；//结果是首字母大写，*p<=>p[0](都是首地址)，hello串从rodata段复制到了栈中局部数组中，p[]为局部数组初始成“hello\0”,等价于char p[6];strcpy(p,"hello");

  * char *p;//空有一个指针P。

    strcpy(p,"hello");    //p指向的空间不一定是有效的可以访问的空间，所以“hello”不能被复制到p指向的空间当中。    

	修改：char buf[10];

	char *p;p=buf;

	内部的char strcpy(char *p, char *q){

	while(*q!='\0'){
		*p=*q;p++;q++;
	}*p='\0';return p;
	}

* 间接引用指针变量时候必须保证变量指向的空间有效即可以访问。

#### 指针的指针：

* int **p;//p是个指针变量，保存了指针变量的地址。int **p,*q;

* int a;a=100; q=&a;p=&q;//保存了指针变量的地址，两个*就是极限了

* int **p,*q; int a;a=100; p=&q;*p=&a;//*p=q,q=&a,->*p=&a;必须保证p指向的空间有效，指针变量也有地址，即加上p=&q.等价于a的首地址放入到q中，q=&a。

#### 指针变量作函数的参数
{% highlight objc %}																	
void swap(int a,int b)
{
	int t;
	t=a;a=b;b=t;
}
int main(void)
{
	int a=1,b=2;
	swap(a,b);
	return 0;
}
//结果还是1，2.没有实现交换的功能，副本改变了，但是本体并没有改变。
改正：swap(int *a,int *b);t=*a;*a=*b;*b=t;swap(&a,&b);

{% endhighlight%}

#### 其他

* 指针指向变量类型，意义在于，1，控制间接引用；2，控制指针移动

* 隐式类型转换：强制--> 主要用在指针上。强制类型转换，（新类型）表达式；int *p;(char *)p;

* 指针做到了数据类型还原成字节，就像汇编一样。编写C，低于9K是不行的。

{% highlight objc %}

void memcpy(void *d, void *s,int n)//转换n个字节从s到d,void *表示泛型指针，即任意类型指针。void *p;*p;不能这么用，p指向的类型不定，所以取的字节数不确定。
{
	char *s1, *s2；
    int i;
	s1=(char *)d;
	s2=(char *)s;
	for(i=0;i<n;i++)
	{
		*s1 = *s2;
		s1++;s2++;    
	}
}

编写一个程序判断大小端：
int test_endian(void)
{
	int a=0x12345678;
	return 0x78==*((char *)&a);
}//返回1小端，0则大端。(char *)一个字节，78；（short *）,两个字节，5678；

{% endhighlight %}

* 指针指向指针变量类型典型用法：控制指针移动
{% highlight objc %}
int a;
int p=&a;
char *p="hello";
p++;//p跳过的不是一个字节，而是p+i*sizeof(type) 个字节，p++跳过的是p指向的变量的字节数。
{% endhighlight %}

* NULL：空指针（相当于宏） #define NULL (void *)0

* 0号地址单元，永远不能访问。清零作用，char *p=NULL;预处理之后等价于，char *p=(void *)0;char *p=NULL；*P=‘A’；会出现段错误，因为在间接引用之前没有保证p所指向的空间是有效的

* 三种段错误：

  * char *p="hello";*p='h';
  
  * char *p;strcpy(p,"hello");
  
  * int **p;int a;*p=&a;

* 指针作参数：间接引用时可以对主函数影响，例如swap()函数；直接引用作参数时对主函数没有作用；void fan(char *p){p=NULL}int main(void){char *p=0x200;fac(p);return 0;}结果还是0x200。

* 指针作返回值：不能返回局部变量的首地址。int *f(void){int a=100;return &a;}不行，&a是无效的，称之为野指针。

* 指针指向变量的类型：

  * 控制指针移动；
  
  * 控制间接引用。

* 泛型指针:

  * 不能间接引用；
  
  * 不能进行移动；
  
  * NULL 4字节；

* typedef与宏的区别：加一个引号。

* C中三种0常量：

  * 0：4bytes

  * '\0':1byte

  * NULL,4bytes;

#### 数组和指针的关系

##### 代码演示

{% highlight objc %}
  int a[10];int *p;p=a;/p=&a[0];
  // 数组名实际上和地址是等效的。
  1.for(i=0;i<10;i++)
  a[i]=i;

  2.for(i=0;i<10;i++)
  *p++=i;

  3.for(i=0;i<10;i++)
  p[i]=i;

  4.for(i=0;i<10;i++)
  *(a+i)=i;

  汇编中，a[2] <=> *（a+2）编译速度提高了但是没有必要。
  *(a+i)=a[i]--*a=a[0];
  (a+i)=&a[i]--a=&a[0];

{% endhighlight %}

* 调用函数时把数组的首地址作为参数传递进来；实参是地址，即可以用指针代替；不同之处：数组名是地址常量，指针是变量；
{% highlight objc %}
int a[10];
int *p;
p=a;    a=p是错误的
*p++;   *a++是错误的
{% endhighlight %}

##### 数组的指针 （指向一个数组）

* int ( *p )[5];//定义一个指向数组的指针，括号是不能省的，p是变量名。

* int a[5];p=&a;(p=a是不行的)，数组整体看成一个对象给p，&符号不是说取数组地址，是告诉编译器将a当作整体来看待，并不会产生额外地址，&a是数组a的首地址。

* void fac(int a[])-->等价于 int *a{return a[0];}  return sizeof(a);恒为4，调用函数时把数组的首地址作为参数传递进来。

* int main(void){int a[]={1,2,3,4,5};f(a);} sizeof(a)--20bytes.因为实参为地址，所以可以用指针代替。void(char *a)

* void bubble_sort(int a[],int n) 因为内部求不出元素的个数，需要n来定义元素的个数。体现了接口设计的思想（科学简洁，扩展性强）。

* 如何设计接口，要科学，与别人的程序对接，要掌握软件开发的基本流程，模块化的设计思想。

* 指针的使用在c中很灵活：
{% highlight objc %}
int a[]={1,2,3,4,5}
printf("%d,%d",*(a+1),*(((int *)(&a+1))-1)); &a+1；//跳转的是整个数组，强制转换成int *指针跳转4bytes，-1之后，跳转到 5上。！！！！！！！！！
{% endhighlight %}

##### 行指针

指向一行的指针。

* 已知 int a[10];-->int * ; int a[2][3];-->int ** 不成立。
* 已知 T a[]; --> T *;
* 已知二维数组可以看成是一维的，每个元素是一行，int a[2][3];-->行型 a[2]; 行 *p；--》数组指针。a [2][3];<--> int ( *p )[3];*p是行指针，3是3个元素，a+1 是第一行。    

#### 接口设计

* 函数中要返回副本，不要把原文件给别人，一般是返回值。

* 传出参数：

  * 必须是指针；

  * 为了传出返回值而存在的；

{% highlight objc %}
strcpy(s1,s2);s1就是传出参数。
typedef struct{int max;int pos;} res_t;
void get_max(int a[],int n,res_t *r);//接口的设计，结构体中可以修改，但是接口不变，做到了兼容，每次函数升级，接口并不用变。

int get_max(int a[],int n,int *pos)
{
	int max, i, p=0;
	max=a[0];
	for(i=0;i<n;i++)
	{
		if(a[i]>max)
			max=a[i];p=i;
		*pos=p;
		return max;
	}
}
int main(void)
{
	int a[]={1,2,3,4,5};
	int m, p;
	m=get_max(a, 5, &p);
	printf("max: %d at: %d",m,p);
	return 0;
}
pos:是传出参数，value-out.
{% endhighlight %}

* 传入参数:函数内部作数值用，不作改变，与传出参数对应，例如：strlen（char *s）;char *s 就是传入参数，调用之前有用，函数的过程中不改变。

* 传入传出参数：冒泡函数 void bubble_sort(int *n,int n);int *n是传入传出参数。

* 接口要有容错能力，容错方案解决：
{% highlight objc %}
void get_max(int a[],int n,res_t *r)
{
	static res_t bakap;
	if(r=NULL) r=&bakap;        
	return r;
}
正常缓冲区的话返回自己的，如果是null 提供缓冲区bakap供返回
{% endhighlight %}

* char * （返回的是字符型的指针） get_common(char *s1,char *s2,char *out,int size) s1,s2是传入参数，out是传出参数，可能是有效或者无效的空间，考虑到安全性，要有size ,这是完整的接口设计。

* 操作系统-->管理者，底层实现者。linux 0成功；-1失败。

* 系统调用，是一个函数，是操作系统的一部分，内核函数，系统调用特权级。自己用的是用户级。API 用户编程接口，上层是应用层，下层是系统层。

* malloc 动态分配内存块（程序运行的进程中）

  * 程序编写时空间大小不确定，要用动态分配函数malloc，动态分配内存。

  * size_t 无符号长整型

  * void *malloc(size_t size)    size 动态分配的字节个数

  * void *动态分配首地址作为返回值（从首地址开始向后的size个字节都是可以使用的），成功的话返回，错误的话就返回NULL（ENOMEM 没有内存）    

  * malloc函数系统调由sbark负责向系统申请内存。

{% highlight objc %}
int main(void)
{
	int n, i;
	int *a;
	scanf("%d",&n);
	a=(int *)malloc(sizeof(int)*n);//动态申请4n个字节。
	for(i=0;i<n;i++)
		scanf("%d",&a[i]);
	for(i=0;i<n;i++)
		printf("%d\n",a[i]);
	free(a);
}

void free(void *ptr)//释放内存的首地址，没有返回值。
free(a);//有申请就要有释放。
{% endhighlight %}

* 系统调用浪费时间，要尽量避免，sbark向系统内核作系统调用。

* 堆（heap）中存malloc动态申请的内存，堆是只升不落的，malloc机制是只申请不退还。


### 文件操作


####文件操作

* FILE *fopen(const char *path,const char *mode)

  * //结构体，用来引用打开的文件。path 路径。mode  权限， 文件模式。

  * w:只写打开文件，文件如果存在的话将自动截到0，即源文件扔掉，自动创建。

  * w+:读写，如果不存在文件自动创建，否则自动截短到0，原文件扔掉。

  * r+:更倾向于读，没有w+的功能，正常读写。

  * 悬挂：a：写入的内容自动追加到结尾，a+：写文件尾，没有的话自动创建，存在的话写在结尾。

  * 返回结构体文件指针，如果失败返回NULL。

  * 读方式打开目录可以，写不可以。

{% highlight objc %}
#include<stdio.h>
int main(void)
{
	FILE *fp;
	fp=fopen("test","r");
	if(fp==NULL)
		perror("error occurs ...");//参数自己定义的字符串，把错误的原因接在串后边
}
ls -l test
chmod u-x text
{% endhighlight %}

* 三种错误：1，没有文件；2，权限不够；3，打开目录（只写）。

* int fclose(FILE *fp) //要关闭的文件的指针，用来关闭文件成功返回0，失败返回-1。如果文件不关闭，长时间的话就打不开了。

* 以字节为单位的读

int fgetc(FILE *stream) 文件结构类型（引用打开文件） 读一个字节作返回值返回（拓展成 int）。

EOF ：文件结束标记，END OF FILE

{% highlight objc %}
char ch;
while(1){
	ch=fgetc(fp);
	if(ch==EOF) break;
	fputc(int c,FILE *stream) //输出一个字节，int中前三个丢掉，最后一个保留。
{% endhighlight %}

* 行为单位的读取

char *fgets(char *s,(用来保存读取的内容)int size,FILE *steam) size 为最多读取的size-1个字节。

从F中读取到s中，size 缓冲区的大小，体现了接口设计的安全性。

vi编译器会自动检测有没有换行，读到换行‘/’ 时结束。

* int fputs(const *s,FILE *steam) 成功返回0，失败返回-1. s指向的空间必须是串，以‘\0’结束。比较长的文件多次读完。

* 块I/O，fread fwrite

size_t（读取的对象个数） fread(void *str,size_t size,(对象字节数)，size_t n,(读取n个) FILE *steam)

size_t(返回实际输出的对象个数)fwrite(char *str，（待输出的内容）size_t size,size_t n,FILE *steam(向哪个文件输出))

{% highlight objc %}
int main(void)
{
	FILE *fp,*out;
	int n,i;
	char buf[5];
	fp=fopen("test","r");
	out=fopen("out","w");
	fread(buf,sizeof(char),5,fp);
	fwrite(buf,sizeof(char),5,out);
	fclose(fp);
	fclose(out);
	return 0;
}
{% endhighlight %}

* 文件系统

FIFL *fopen(const char*path, const char *mode); fclose(FILE *fp); 

文件I/O 库函数通过应用编程接口API实现 库函数调用API

操作系统的特点，长期运行， 高权级； 操作系统提供给我们底层的抽象，应用层使用接口就行了，系统调用就是这个接口，利用它就可以操控底层的资源了。系统调用操作内核数据结构，操作底层硬件资源，库函数封装了系统调用，系统调用实现库函数功能。


------------------------------------------------------------------------------------------
#### 这一篇整理的是之前读书时候的笔记，参考<a href="http://songjinshan.com/akabook/zh/index.html">LINUX一站式编程</a>
