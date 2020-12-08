---
title: 《Redis设计与实现》学习笔记
date: 2020-12-08 11:51:42
categories:
- Redis
tags:
- Redis
---

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208112728885.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<!--less-->

## 概述
本篇整体讲一讲redis，基本数据结构？有何作用？适用场景？

## Redis的基本数据结构：
### （1）SDS(simple dynamic string)
SDS的定义：位于sds.h/sdshdr


```c
Struct sdshdr {
	//记录buf数组中已使用字节的数量
	//等于SDS所保存字符串的长度
	int len;

	//记录buf数组中未使用字节的数量
	int free;

	//字节数组，用于保存字符串
	Char buf[];
};
```

C中的字符串实现是通过字符数组，以空字符’\0’结尾来标识字符串的结束。
缺点：
1)需要遍历数组，来得出字符串长度
2)当字符串扩容超过定义的数组长度时，会产生缓冲区溢出
3)当缩短字符串时，会产生内存泄漏（分配到的内存大于所需内存）
4)每修改字符串一次，就会造成内存重分配一次（因为重写定义数组）
5)只能保存文本数据，不能保存二进制数据。

<font color=red>Redis没有直接使用上述C语言传统的字符串表示，而是自己构建一种名为简单动态字符串（SDS)的抽象类型，并作为默认字符串表示。</font>

SDS优点:
1）常数复杂度获取字符串长度。

2） 杜绝缓冲区溢出。

3） 减少修改字符串长度时所需的内存重分配次数。

4） 二进制安全。

5） 兼容部分C字符串函数。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208111019635.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208111026223.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

 

### （2）redis中的链表（list）:
是双端链表、带表头指针和表尾指针、带链表长度计数器（len），redis中的链表节点由listNode结构体表示，而链表则有list表示之。
Redis中链表的功能：列表键、发布与订阅、慢查询、监视器

### （3）字典
Redis中字典使用哈希表作为底层实现，一个哈希表里面可以由多个哈希表节点，而每个哈希表节点就保存了字典中的一个键值对。

Redis的哈希表使用<font color=red>**链地址法**</font>解决hash冲突。

Rehash:哈希表保存的键值对会逐渐地增多或者减少，为了让哈希表的负载因子（load factor）维持在一个何理的范围之内，当哈希表保存的键值对数量太多或者太少时，程序需要对哈希表的大小进行相应的扩展或者收缩。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020120811115367.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208111242451.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


### （4）跳跃表（skipList）
**跳跃表是一种有序结构，它通过在每个节点中维持多个指向其他节点的指针，从而达到快速访问的目的。**（<font color=red>平均O(logN)，最坏O(N)的查找复杂度</font>）
大多数情况下，跳跃表的效率可以和平衡树相媲美，并且因为因为跳跃表的实现比平衡树来得更简单，所以有不少程序都使用跳跃表来代替平衡树

<font color=red>Redis使用跳跃表作为有序集合（zset）的底层实现之一。</font>Redis只在两个地方用到跳跃表，另一个是在集群节点中用作内部数据结构。

跳跃表的实现：其分为跳跃表节点（定义于：redis.h/zskiplistNode）和跳跃表（定义于：redis.h/zskiplist），zskiplistNode结构用于表示跳跃表节点，而zskiplist结构用于保存跳跃表的相关信息，比如节点的数量，以及指向表头节点和表尾节点的指针等。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208111407548.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208111427775.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


Zskiplist：用于维护由多个跳跃表节点组成的跳跃表。
结构如下：


```c
Typedef struct zskiplist {
	//表头节点和表尾节点
	Struct skiplistNode *header, *tail;

	//表中节点的数量
	Unsigned long length;

	//表中层数最大的节点的层数
	Int level;
} zskiplist;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208111648393.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

### （5）整数集合（intset）
**整数集合（intset）是集合键的底层实现之一，当一个集合只包含整数值元素，并且这个集合的数量不多时，redis就会使用整数集合作为集合键的底层实现。** 
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208111719998.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208111741541.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
Length属性记录了整数集合包含的元素数量，也即是contents数组的长度。
虽然intset结构将contents属性是声明为int8_t类型的而数组，但实际上contents数组并不保存任何int8_t类型的值，contents数组的真正类型取决于enconding属性的值。

### （6）压缩列表（ziplist）
压缩列表（ziplist）是列表键和哈希键的底层实现之一。当一个列表键只包含少量列表项，并且每个列表项要么就是小整数值，要么就是长度比较端的字符串，那么redis就会使用压缩列表来做列表键的底层实现。

压缩列表是redis为了节约内存而开发的，是由一系列特殊编码的<font color=red>**连续内存块**</font>组成的顺序型（sequential）数据结构。一个压缩表可以包含任意多个节点（entry），每个节点可以保存一个字符数组或者整数值。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208111851375.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208111909305.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


### （7）对象
前面陆续介绍了redis中用到的主要的数据结构：简单动态字符串（SDS）、双端链表、字典、压缩列表、整数集合等。Redis并没有直接使用这些数据结构来实现键值对数据库，而是基于这些数据结构创建了一个对象系统，这个系统包含了<font color=orange>**字符串对象、列表对象、哈希对象、集合对象、有序集合对象**</font>这五种类型的对象。

每当在数据库中创建一个键值对时，会创建两个对象，一个对象用作键值对的<font color=red>键（键对象）</font>，另一个对象用作<font color=red>键值对的值（值对象）</font>。

Redis中的每一个对象都由一个redisObiect结构表示，该结构中和保存数据有关的三个属性分别是type属性、encoding属性和ptr属性：


```c
typedef struct redisObject {
	//类型
	Unsigned type:4;

	//编码
	Unsigned encoding:4;

	//指向底层实现数据结构的指针
	Void *ptr;

	// …
} robj;
```

对象的type属性：该属性记录了对象的类型：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020120811214172.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

对象的ptr指针指向对象的底层实现数据结构，而这些数据结构由对象的encoding属性决定。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208112209177.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020120811234962.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
#### 1.字符串对象
字符串对象编码可以使用int,raw,embstr

当字符串对象保存的是整数值时，字符串对象的编码设置为int。

当字符串对象保存的是一个字符串并且长度小于等于32字节，则用embstr编码，大于32字节则会用raw编码。

注意：long double类型的浮点数在redis中是作为字符串保存的。

Int编码的字符串对象和embstr编码的字符串对象在条件满足的情况下，会被转化为raw编码的字符串对象。

Redis使用c语言实现的，而c语言中，字符串是用字符数组表示的。所以，将底层实现的字符数组转化为表示层的String，redis数据库需要一些东西来辅助之，redis每次给字符串分配内存时，都会分配更过的内存，因为redis种的string是的长度是可以动态变化的，当string增长时，若没有超过所分配的内存大小，则不需要扩容，否则的话就需要进行扩容了。<font color=red>**即redis实行动态扩容机制，避免了频繁的内存分配。**</font>
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208112455134.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


Redis没有为embstr编码的字符串对象编写任何的修改程序（只有int,raw编码有），所以embstr编码的字符串对象实际上是只读的。当我们对embstr编码的字符串对象执行修改命令时，程序先会将对象的编码从embstr转换为raw，然后再执行修改命令。
#### 2.列表对象
列表对象的编码可以是ziplist或者linkedlist
Ziplist编码的列表对象使用压缩列表作为底层实现，每个压缩列表节点保存了一个列表元素。
Linkedlist编码的列表对象使用双端链表作为底层实现，每个双端链表节点都保存一个字符串对象，而每个字符串对象都保存一个列表元素。

什么时候使用Ziplist编码：满足以下两个条件：
1.列表对象保存的所有字符串元素长度都小于64字节
2.列表对象保存的元素数量小于512字节；
不能满足这两个条件的列表对象需要使用linkedlist编码。

#### 3.哈希对象
哈希对象的编码使用ziplist或者hashtable

Ziplist编码的哈希对象使用压缩列表作为底层实现，每当有新的键值对要加入到哈希对象时，程序会先将保存了键的压缩列表节点推入到压缩列表表尾，然后再将保存了值得压缩列表节点推入到压缩列表表尾，因此：
（1）保存了统=同一键值对得两个节点总是紧挨在一起，保存键得节点在前，保存值得节点在后；
（2）先添加到哈希对象中的键值对会被放在压缩列表的表头方向，而后来添加到哈希对象中的键值对会被放在压缩列表的表尾方向。

Hashtable编码的哈希对象使用字典作为底层实现，哈希对象中的每个键值对都使用一个字典键值对来保存：
（1）字典的每个键都是一个字符串对象，对象中保存了键值对的键；
（2）字典的每一个值都是一个字符串对象，对象中保存了键值对的值。

#### 4.集合对象
集合对象的编码可以是intset或者hashtable。

Intset编码的集合对象使用整数集合作为底层实现，集合对象包含的所有元素都被保存在整数集合里面。

Hashtable编码的集合对象使用字典作为底层实现，字典的每个键都是一个字符串对象，每个字符换对象包含了一个集合元素，而字典的值则全部被设置为NULL。


#### 5.有序集合对象
有序集合的编码可以是ziplist或者skiplist。

Ziplist编码的压缩列表对象使用压缩列表作为底层实现，每个集合元素使用两个紧挨在一起的压缩列表节点来保存，第一个节点保存元素的成员（member），而第二个元素则保存元素的分值（score）。压缩列表内的集合元素按分值从小到大进行排列，分值较小的元素被放置在靠近表头的方向，而分值大的元素则被放置在靠近表尾的方向。

```c
Typedef struct zset {
	Zskiplist *zset;
	Dict *dicr;
} zset;
```

#### 内存回收：
C语言不具备自动内存回收功能，所以redis在自己的对象系统中构建了一个引用计数技术实现的内存回收机制。每个对象的引用计数信息由redisObject结构的refcount属性记录：


```c
typedef struct redisObject {
	//类型
	Unsigned type:4;

	//编码
	Unsigned encoding:4;

	//指向底层实现数据结构的指针
	Void *ptr;

	// …

	//引用计数
	int refcount;

	// …
} robj;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208112632317.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

#### 空转时长：
除了前面所介绍的type,encoding,ptr和refcount四个属性之外，redisObject结构包含的最后一个属性为lru属性，该属性记录了最后一次被命令程序访问的事件。

OBJECT IDLETIME命令可以打印出给定键的空转时长，这一空转时长就是通过将当前事件减去键的值对象的lru时间计算得出的。

键的空转时长可作为内存回收算法的指标，即当服务器占用的内存数超过maxmemory选项所设置的上限值时，空转时长较高的那部分键会优先被服务器释放，从而回收内存。

#### 对象共享：
目前来说，redis在初始化服务器时，会创建一万个字符串对象，这些对象包含了从0到9999的所有整数值，当服务器需要用到值为0到9999的字符串对象时，服务器就会被用到这些共享对象，而不是创建新的对象。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208112705617.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
上方内容也可参考：[Redis设计与实现 读书笔记](https://blog.csdn.net/u012888052/article/details/80397344)

参考文献：《Redis设计与实现》黄健宏-著