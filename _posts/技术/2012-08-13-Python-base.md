---
layout: post
title: Python整理
category: 技术
tags: Lang
keywords:
description:
---

本文之前是按天积累的，顺序有点混乱，并且内容不是很全，都是挤出点，从某度我的个人空间记录的：
<a href="http://hi.baidu.com/wenjiashe521/item/8579f2bd2f99454dba0e1243">点击查看</a>

强类型 弱类型(Python) a=1 a='2' type() dir() 压缩显示哪些操作help()  -----> 三个内置自引函数； 

变量运行的时候类型可以随时改变；

数据容器，最重要的四种集合：

* list [ ]便于增加改变元素；
* tuple( )元素不可操作重查询 效率高于list；
* dict{key:val}；
* set。

基本类型：string True/False number None(NULL) ；

切片操作，比较灵活；

LIST：可以嵌套，与类型无关；

##### 函数式编程：
{% highlight objc %}
map()
>>>a=[1,2,3,4,5]
>>> map(lambda x:x*2,a)
[2, 4, 6, 8, 10]
{% endhighlight %}

reduce() 收敛操作 reduce(lambda x,y:x+y, d) 对d进行收敛操作即迭代相加:
{% highlight objc %}
>>>d=[1,2,3,4,5]
>>> reduce(lambda x,y:x+y, d)
15
{% endhighlight %}

filter()配合前两者使用
{% highlight objc %}
>>>a=[1,2,3,4,5]
>>> filter(lambda x:x%2+1,a)
[1, 2, 3, 4, 5]
>>> filter(lambda x:not x%2,a)
[2, 4]

index()
>>> b=[1,3,4,56,6,6]
>>> b
[1, 3, 4, 56, 6, 6]
>>> b.index(56)
3
>>> b.index(6)
4

count()
>>> b
[1, 3, 4, 56, 6, 6]
>>> b.count(4)
1
>>> b.count(6)
2
{% endhighlight %}

set()不支持索引操作
{% highlight objc %}
>>> a=[1,1,1,1,1,1,2,3,4,5,4]
>>> b=a
>>> set(b)
set([1, 2, 3, 4, 5])
{% endhighlight %}

元组不可操作，但其检索效率高，性能比list好，也就是说，不提共动态内存管理的功能

三引号的时候，文档字符串帮助信息，也可以当注释。

pass 什么也不做，为了满足排版的操作，相当于占行符。

None类型：是一个特殊的常量，表示出错；

Python中，除0以外，其他都是真，但是假也包含很多------None,0,"",[],(),{} 空的都为假；

sequence 包括 string,list,tuple。他们都有以下一些通用的操作：

判断某个object是不是在一个sequence中。
{% highlight objc %}
>>> x=12
>>> l=[12,13]
>>> if x in l : print "x is in l"
... 
x is in l
len(sequence)
seq[i]
{% endhighlight %}

通过冒号取子sequence,seq[start:end];

用+号表示连接两个sequence;

用*号表示重复sequence "a"*3表示aaa;

可以使用list comprehension;
{% highlight objc %}
>>> a
[1, 2, 3, [4, 5, 6]]
>>> for i in range(0, len(a), 1):
...     print a[i]
... else:
...     print "run over"
... 
1
2
3
[4, 5, 6]
run over

>>> a, b = 1, 'a'
>>> a
1
>>> b
'a'
{% endhighlight %}

类里边使用的时候要有self;

所有的成员函数第一个参数是self 函数里边也要注意;

局部函数中声明全局变量使用：global 声明;

{% highlight objc %}
>>> class a:
...     def __init__(self, c=1, d=2):
...             self.d = d
...             self.c = c
...     def printa(self):
...             self.f='e'
...             print self.c, self.d, self.f
... 
>>> if __name__=='__main__':
...     x=a()
...     x.printa()
... 
1 2 e
{% endhighlight %}

多线程：

继承类--->threading.Thread;

threading. Thread.__init__(self);

def run(self);
    
架构 分层 :

Django:model orm class 支持对象查询 持久化操作 对象操作 table template 模板 view control 处理网页。

