---
title: 《Redis学习笔记（一）》
date: 2021-02-21 18:12:01
categories:
- Redis
tags:
- Redis
---

![](http://qiniusave.codeyu.cn/redis.jpg)

<!--less-->

---

## Redis基础知识
###  启动Redis服务：
双击：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301162709871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

即可启动：

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301162732317.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

PID为进程号、Port为端口号。

### 使用Redis刻客户端连接Redis
打开Redis客服端（<font color='red'>Redis服务窗口始终处于打开状态</font>）：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030116285011.png)

弹出界面：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301162855500.png)

测试Redis客户端连接Redis成功与否？
输入：ping
返回：pong 则表明连接成功。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301162904183.png)


### 查看Redis中所有已存在的键（Key）
命令：keys *
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301162944356.png)


### 选择不同的数据库
因为Redis有16个数据库，默认使用的是0号数据库，通过命令：
Select (序号0~15)
如：select 7
即可选择不同的数据库了。

### 查看某一个数据库中存储的数据的条数
命令：DBSIZE
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301163008587.png)


### 清空某一个数据库中的所有数据
命令：flushdb

### 清空所有数据库中的所有数据
命令：FLUSHALL

### 为什么Redis的端口是6379？
来历：
MERZ在九宫格输入法上对应6379

### Redis是单线程的！
Redis特别块！官方表示：Redis基于内存操作，<font color="red">CPU不是Redis性能瓶颈，Redis性能瓶颈是根据机器的内存和网络带宽，既然可以使用单线程来实现，就使用单线程了</font>。所以就使用了单线程。

### 那Redis为什么单线程还这么快？
Redis是C语言写的

1.误区1：高性能服务器一定是多线程的！
2.误区2：多线程（CPU上下文会切换！）一定比单线程效率高！
速度：CPU>内存>硬盘
<font color="red">核心：</font>Redis是将所有的数据全部放在内存中，所以说使用单线程去操作效率就是最高的。因为多线程（CPU上下文切换是耗时的操作！），而对内存系统来说，如果没有上下文切换效率就是最高的！多次读写都是在一个CPU上的，在内存情况下，这个就是最佳答案！<font color="red">即在内存模型情况下，使用单线程！</font>

### 判断某一个键是否存在
命令：EXISTS (键)

### 移除数据库中的某一个键
命令：move (键)

### 设置键的过期时间
命令：EXPIRE (键) (数值，单位为s)

### 查看键还有多少时间后会过期
命令：ttl (键)
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301163720179.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)


### 查看数据类型
命令：type (键)
### 往String类型的数据后追加数据
命令：APPEND (键) (字符串)

### 遇到不会的命令可在官网查看

[参考文档](http://www.redis.cn/commands.html)


## Redis中五种基本数据类型
## String类型的学习：
90%的Java程序员只会使用redis的String类型，但是其还有很多类型，我们应该根据不同的场景而选用不同的类型进行使用。

### APPEND往String类型的数据后追加数据
命令：APPEND (键) (字符串)

### STRLEN查看String类型数据的长度
命令：STRLEN
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301163928438.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

 

### incr实现i++/i--功能的命令
命令：incr (键)
命令：decr (键)
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301163941855.png)


### INCRBY实现加一个给定值的功能的命令
命令：INCRBY (键) (给定值)
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301163955425.png)


### GETRANGE获取一个范围内的字符串
命令：GETRANGE (键) (起始索引) (结束索引)
相当于Java中的subString()方法

GETRANGE (键) 0 3   相当于获取[0,3]闭区间内的
GETRANGE (键) (起始索引) -1    此命令为获取全部的字符串

### SETRANGE进行范围替换
命令：SETRANGE (键) offset value

SETRANGE (键) 1 xx


### setex （set with expire） #设置过期时间
命令：setex (键) second value
如：setex key1 30 “hello”   含义：设定键key1的值为字符串“hello”，并且该键值对的过期时间为30s

### setnx（set if not exist） #如果不存在则设置（<font color="red">在分布式锁中常使用</font>）
命令：setnx key value
如下图：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301164730729.png)

就是如果一个键不存在，则可以成功设置以该键作为键的键值对，否则不能设置。


### mset、mget  批量设置值和批量获取值
命令：mset k1 v1 k2 v2 […]

### msetnx   #批量进行“如果不存在则设置”


如何新建一个对象
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301164807359.png)

<font color="red">Redis中可以通过冒号来巧妙的实现各种key：如上：user:{id}:{field}</font>
那么：
user:1:name、user:2:name、user:3:name都是键
article:10000:views  当前第10000篇文章的浏览量，<font color="red">所以通过改变{id}值就可以实现key的复用了。</font>


### getset命令：即先get再set
如果不存在则设置，如果存在则设定新的值（可用于更新操作）
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301164906571.png)



### String类型的使用场景
String类型的使用场景:value除了是我们的字符串还可以是我们的数字！
	计数器
	统计多单位数量
	粉丝数
	对象缓存存储

## List
在Redis里面，我们可以把list玩成：栈（单边进出）、队列（一边进一边出）、阻塞队列（双端进出）！


### LPUSH/RPUSH 和 LRANGE 用于往列表中添加数据和获取列表某一个区间内的数据
命令：LPUSH
作用：将一个值或者多个值插入列表的头部（左）。
命令：RPUSH
作用：将一个值或者多个值插入列表的尾部（右）。

并且通过LRANGE取数据时，是倒着拿数据的，也就是最后放入list中的数据最先取出，如下方的three

所以通过这些操作就可以实现栈、队列、阻塞队列等功能。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301164938975.png)


### LPOP和RPOP从左边/右边移除一个数据
命令：LPOP key

### LINDEX 通过下标获取List中的某一个值
命令：lindex key value(下标value)


### Llen 返回List的长度
命令：Llen key

### Lrem 移除指定的值
命令：Lrem key value(移除个数) value
Lrem list 1 one   ：移除list列表中的1个one值


### trim 修剪操作，在List中则是Ltrim：用于截断操作
命令：Ltrim key 【范围】(指保留下的元素范围)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301165028390.png)


### rpoplpush 移除列表的最后一个元素，并将它添加到另一个列表的头部
命令：rpoplpush source destination
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301165039793.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)


### Lset 和String类型中的set命令不同，在List中使用lset命令并不能创建一个key，List中只有lpush/rpush能够创建key，所以在使用Lset命令之前先得使用lpush/rpush创建出key才行
命令：lset key(必须已存在) index value
作用：将指定下标处的值设为所提供的值（相当于update操作）


### Linsert/Rinsert 往List中插入值
命令：Linsert key before|after(往前插入还是往后插入) pivot(基准点) value
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301165114978.png)


### exists 判断键是否存在
命令：exists key

### List总结：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301165129555.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)






## Set（集合）
Set中的值不能重复！

### sadd 往Set中添加元素
命令：sadd key value

### smembers 打印出一个Set中的所有元素
命令：smembers key

### sismember (is member 判定)即判断某个元素是否属于某个Set
命令：sismember key value

### scard 获取Set集合中的元素的个数
命令：scard key

### srem 移除Set中的某一个元素
命令：srem key value

### srandmember 随机返回Set集合中的一个元素
命令：srandmember key 随口抽取出一个元素
命令：srandmember key value 随口抽取出指定个数的元素

### spop 随机移除元素
命令：spop key


### smove 将一个集合中的某些元素移动到另外一个集合中去
命令：smove source destination member
smove key1 key2 value

### sdiff 返回差集
命令：sdiff key1 key2
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301165233624.png)

sdiff命令会以第一个key为基准，返回<font color="red">在第一个集合中的且不属于第二个集合的元素</font>

### sinter 求交集（共同关注/好友）
命令：sinter key1 key2

## Hash（哈希）
Map集合，key-map:也就是key-<field,value>:此时的值是一个map集合！此处的field实际上也就是value的key罢了
本质上和String类型没有太大区别，还是一个简单的key-value!

### hset 新建一个哈希
命令：hset key field value

### hget 获取hash中filed字段对应的值
命令：hget key field

### hmset 设置多个hash的field和value值对
命令：hmset key field1 value [field value …]


### hmget 获取hash中多个field的值
命令：hmget key field1 [field…]

### hgetall 获取哈希中所有的<field,value>键值对
命令：hgetall key

### hdel 删除哈希指定的key字段，field删除了，对应的value也就没了
命令：hdel key filed1 [field …]

### hlen 获取哈希表的字段数量，也就是<field,value>键值对的个数
命令：hlen key

### HEXISTS 判断哈希的某个key对应的某个field是否存在
命令：HEXISTS key field


### hkeys 只获得哈希的所有的field
命令：hkeys key

### hvals 只获得哈希所有的值
命令：hvals key

### hincrby/hdecrby 给哈希中的field对应的value加上/减去一个固定值
命令：hincrby key field value

### hsetnx (hset if not exist) 如果不存在则会成功执行hset命令，如果存在，则不会执行hset
命令：hsetnx key field value


### 哈希应用场景总结：
Hash用于存储变更的数据，如将一个user作为一个hash的key，然后这个user里可以存name,age等字段，也就是将name和age作为field。
比如：hset user:1 name “junjun”   hset user:1 age 24
<font color="red">此处是将user:1作为hash的key的。</font>
Hash用于存储变更的数据，尤其是用户信息之类的，经常变动的信息！<font color="red">hash更适合于对象的存储，String更加适合字符串存储！</font>

## Zset（有序集合）
Zset在插入值时，需要给定一个score用于Zset中元素的排序

### zadd 往Zset中添加元素
命令：zadd key score value

### zrange 获取一定范围内的元素
命令：zrange key start end
比如：zrange key 0 1  / zrange key 0 -1

### zrangebyscore 通过score排序返回排序后的数据
命令：zrangebyscore key min max
这样写可以返回所有的数据：zrangebyscore key -inf +inf
这样写会一并返回对应的scores：
zrangebyscore key -inf +inf withsocres

### zrem 移除元素
命令：zrem key vlaue
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301165522195.png)


### zcard 获得Zset中元素个数
命令：zcard key
### zcount 获取指定区间的成员数量
命令：zcount key min max


### Zset应用场景总结：
1.可用于存储班级成绩，工资表排序等。
2.又比如对消息进行加权，普通消息设置其权值为1，比较重要的消息设置其权值为2。
3.排行榜应用实现，比如B站实现的每日最热门。


## Redis中三种特殊数据类型
## geospatial 地理位置
微信附近的人、打车距离计算？
Redis的Geo在Redis3.2版本就推出了！这个功能可以推算地理位置的信息，两地之间的距离，方圆几里的人！

此数据类型只有六个命令：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301165621531.png)

### geoadd 将指定的地理空间位置（经度、纬度、名称）添加到指定的key中
命令：geoadd key (经度) (纬度) value
比如：
geoadd china:city 116.40 39.90 beijing

geoadd china:city 121.47 31.23 shanghai

有效的经度从-180度到180度。
有效的纬度从-85.05112878度到85.05112878度。

### geopos 从key里返回所有给定位置元素的位置（经度和纬度）
命令：geopos key value

比如：geopos china:city beijing

### geodist 两个城市之间的距离
命令：geodist key city1 city2
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301165647497.png)

将单位变成千米：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301165651302.png)



### georadius 以给定的经度纬度为中心，找出某一半径内的元素


## Hyperloglog
什么是基数？
A{1,3,5,7,8,7}
B{1,3,5,7,8}
基数（不重复的元素个数）=5，可以接受误差！

Redis2.8.9版本更新了Hyperloglog数据结构！

Redis Hyperloglog 基数统计的算法

<font color=red>Redis Hyperloglog优点：扎用的内存是固定的，2^64不同元素的基数，只需要花费12KB内存！从内存角度来比较的话Hyperloglog首选项！</font>

网站的UV（一个人访问一个网站多次，但是还是算作一个人！） 传统的实现方式：set保存用户id，然后通过统计set中的元素数量来得到网站的访问人数。这个方式如果保存大量的用户id，会特别的占用内存！我们的目的是计数，而不是保存用户id。


Hyperloglog总共三个命令：
### pfadd 添加元素
命令：pfadd key element [element …]

### pfcount 统计Hyperloglog中存储的不重复元素的个数
命令：pfcount key

### pfmerge 将两个集合合并为一个新的集合
命令：pfmerge key(新的集合的键名) key1(已存在的集合的键名) key2(已存在的集合的键名)
并且合并过程中会剔除两个集合的重复元素！

Hyperloglog的错误率为0.81%，所以如果允许错误的话，一定就可以使用Hyperloglog了！不过不允许容错，则使用set或者自己的数据类型


## Bitmaps 位图
<font color=red>位存储！</font>
统计用户信息，活跃，不活跃！登录、未登录！打卡！
像上述的只有两种状态的，都可以使用Bitmaps！
Bitmaps位图，数据结构！都是操作二进制位来进行记录，就只有0和1两种状态！

比如统计用户打卡情况：“已打卡”“未打卡”，统计365天的打卡情况，每天的数据只需要一个数据位即可存储！
365天=365bit=365bit/8byte=46个字节左右！（很节约内存）

Bitmaps总共三个方法：
### setbit 
命令：setbit key offset(就是指哪一个位) 0/1
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301165803871.png)

设定一个星期的7天的打卡情况，0代表未打卡，1代表打卡。

### getbit 查看某一天是否打卡
命令：getbit key offset(哪一个位)

### bitcount 统计位图中位为1的个数