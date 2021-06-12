---
title: 《Redis学习笔记（二）》
date: 2021-02-24 15:09:42
categories:
- Redis
tags:
- Redis
---

![](http://qiniusave.codeyu.cn/redis.jpg)

<!--less-->

---

## 事务
MySQL：要么同时成功，要么同时失败，原子性！
<font color=red>但是：Redis单条命令是保证原子性的，但事务不保证原则性！</font>

Redis事务本质：一组命令集合！一个事务中的所有命令都会被序列化，在事务执行过程中，会按照顺序执行！
Redis事务的特性：<font color=red>一次性、顺序性、排他性！</font>
---------- 队列 set set set 执行 ----------

<font color=red>Redis事务没有隔离级别的概念！</font>

Redis事务：
1.	开启事务（multi）
2.	命令入队（命令）
3.	执行事务（exec）
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170431624.png)


### Redis事务就只有两个命令：
#### mutli 开启事务
命令：multi


#### exec 执行事务
命令：exec

<font color=red>每次执行完事务之后需要使用multi命令重新开启事务！</font>
### discard 放弃事务，即执行multi命令后发现不想执行事务了，则可以使用discard来放弃事务
命令：discard

类比Java，事务中存在的错误：
编译型异常（代码有问题！命令有错！），出现此类型异常时，事务中所有的命令都不会被执行！（比如在事务中代码错误之类的原因）
运行时异常（比如：1除以0），如果事务队列中存在语法性，那么执行命令的时候，其他命令是可以正常执行的，错误命令抛出异常！（比如给字符串类型自增1产生的异常）

## Redis实现悲观锁和乐观锁
### 悲观锁
	很悲观，认为什么时候都会出问题，无论做什么都会加锁！



### 乐观锁
	很乐观，认为什么时候都不会出问题，所以不会上锁！更新数据的时候判断一下，在此期间是否有人修改过这个数据，version!
	获取version
	更新的时候比较version

### Redis 监视测试 watch指令：监视一个对象 下方：测试多线程修改值，使用watch可以当作redis的乐观锁操作！

正常执行成功：下图中监视money对象，事务正常执行（单线程执行）
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170630107.png)


<font color=red>用Reids命令加锁，就是用一个watch命令就好了：使用watch可以当作redis的乐观锁操作</font>
在此图的基础上： 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170701588.png)

又继续监视money，并且开启一个事务但不exec:
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170710689.png)

然后打开第二个redis-cli.exe终端，输入如下命令：（模拟两个线程）
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170716525.png)

这就是一种情况：第一个线程执行事务并即将准备执行exec指令时，第二个线程突然将money的值改为1000了，此时的money发生变动了，它的变动会被第一条线程中的watch money给监视到，这个watch就会告诉其下方的事务，“当前的money值被修改了，事务执行会失败”。
如下图：事务失败
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170727757.png)


### unwatch 解除监视
命令：unwatch
发生上述问题之后，我们通过unwatch指令接触监视，然后再次使用watch money来获取money的新的值，也就是1000，实现重新监视
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170809458.png)


## Jedis
我们要使用java来操作Redis，所以就要使用客户端，最简单的就是Jedis

什么是Jedis，它是Redis官方推荐的java连接开发工具！即使用Java操作Redis的中间件！如果你要使用java操作redis，那么一定要对Jedis十分的熟悉！

### 如何创建多模块项目，见狂神redis视频第23级的3分20秒处:[https://www.bilibili.com/video/BV1S54y1R7SB?p=23&spm_id_from=pageDriver](https://www.bilibili.com/video/BV1S54y1R7SB?p=23&spm_id_from=pageDriver)
1.创建一个空的工程项目
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170905378.png)

完成之后。
2.新建该工程的子模块
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170912247.png)

3.点击new module
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170917357.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170924803.png)


4.选择管理工具（按照习惯还是选择maven）
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170929288.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

5.写上模块的Name以及GroupId，还有Location：因为是作为工程的子模块，所以应该放在工程目录下
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030117093613.png)

6.点击完成之后，选择“priject”选定JDk版本
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170948676.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170952689.png)


点击“应用”并“确定”。
7.将idea本身的Java编译器版本改为与当前java jdk版本对应的版本（因为idea默认的java编译器版本是1.5版本）（比如我的都是改为13版本）
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301170958413.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171005943.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)


点击“应用”和“确定”
8.自此之后，一个含有子模块的Java工程项目新建完毕，之后当要建立更多的子模块时，从第2步开始即可。


### 1.Pom.xml文件中导入jedis包
在上述创建的工程项目中导入jedis的包：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171024651.png)


### 2.编码测试
	连接数据库
	操作命令
	断开连接！

<font color=red>狂神视频中的该测试案例连接的是本地的Windows版本的Redis，在实际工作中如果需要连接远程的Redis，再去博客搜索如何连接远程Redis即可！</font>
### 连接数据库：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171052308.png)

输出：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171058733.png)


常用API
String
List
Set
Hash
Zset
前面已经讲过Redis的基本操作指令了，Jedis的API与其基本一致！


## Springboot整合
Springboot操作数据：spring-data jpa jdbc mongodb redis!
SpringData也是和springBoot齐名的项目！
<font color=red>说明：在SpringBoot2.x之后，原来使用的jedis被替换为了lettuce</font>
<font color=red>如果公司使用的SpringBoot还是1.x版本的话，那么底层仍然是Jedis。</font>
Jedis:采用的是直连，多个线程操作的话是不安全的，如果想要避免不安全，则需要使用jedis pool连接池来解决（由此带来的问题:比如线程数量太多，redis-server就会特别大）！更像BIO模式
Lettuce:采用netty，实例可以在多个线程中进行共享，不存在线程不安全的情况！可以减少线程数据量，更像NIO模式


<font color=red>SpringBoot所有的配置类，都有一个自动配置类（比如redis的就是：RedisAutoConfiguration）
自动配置类都会绑定一个properties配置文件（比如redis的就是：RedisProperties）</font>

源码分析<font color=red>RedisAutoConfiguration</font>：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171230185.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

<font color=gyello>上述的RedisTemplate<Object,Object>有两个Object，在使用过程中经常需要强制转换类型，转化为所需要的类型，一般是转换为<String,Object>。</font>
默认的RedisTemplate没有过多的设置，Redis对象都是需要序列化！

上图中的StringRedisTemplate类型，因为String类型是Redis中最常用的类型，所以单独提出来了一个bean!

上图中的注解@ConditionalOnMissingBean注解的作用就是说如果开发者没有重写上述redisTemplate()方法的话，则默认使用上述的redisTemplate()方法。


### 整合测试一下
1.	导入依赖
2.	配置连接
3.	测试！

### 1.导入依赖
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171321899.png)


### 2.配置连接
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171327202.png)

对于配置连接类如何使用，见RedisProperties类，<font color=red>该类以及所有的配置类都可以在org.springframework.boot:spring-boot-autocinfigure的jar包的META-INF包下的spring-factories中寻找，在其中搜索Redis，就可以找到RedisAutoConfiguration类，按住ctrl+鼠标左键即可定位该类的定义，并在定义中又可以找到RedisProperties类，在该类中就可以找到如何配置redis的相关参数了。</font>

### 3.测试
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171403649.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171411716.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

<font color=gree>下图为序列化配置相关的：</font>
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171443300.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

<font color=blue>默认的序列化方式为：jdk序列化，而jdk序列化就会让我们的字符串转义，所以我们可以通过自定义的方式来使用Json序列化，从而解决字符串转移的问题！所以需要我们自己定义配置类</font >
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171527749.png)


### 如何自己定义配置类
**在RedisAutoConfiguration配置类中表明，如果我们自己去定义一个配置类的话，则默认的配置类就会自动失效。因为有@ConditionlOnMissingBean注解**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171544845.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)


## 自定义RedisTemplate
### 1.存入redis中的java对象都需要实现Serializable接口。但可以不主动序列化为Json字符串。但是实际开发中最好主动序列化为Json字符串，再存入redis中
User类如果不实现该Seraliable接口，则会报错：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171605719.png)

所以必须实现接口：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171610735.png)


### 2.但是即使pojo类实现了Seraliable接口，其序列化时会使用默认的序列化，如果我们不想使用默认的序列化，那么就需要我们去配置RedisConfig类了
指定序列化方式：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171621173.png)

setKeySerializer()方法的参数有如下图这么多种：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171628326.png)

Jackson2JsonRedisSerializer就是序列化为Json，比如：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171634401.png)


### 编写一个自己的redisTemplate方法，在企业中拿去就可使用
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171651662.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301171658534.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

 

定制自己的RedisConfig并重新定义redisTemplate()方法，从而避免产生的key乱码。（因为采用默认的序列化规则时，中文和对象都会键值乱码）
### 中间小结：所以的Redis操作，其实对于Java开发人员来说，十分的简单，更重要的是要去理解redis的思想和每一种数据结构的用处和作用场景！

### Jedis与RedisTemplate的区别
Jedis是Redis官方推荐的面向Java的操作Redis的客户端，而RedisTemplate是SpringDataRedis中对JedisApi的高度封装。
SpringDataRedis相对于Jedis来说可以方便地更换Redis的Java客户端，比Jedis多了自动管理连接池的特性，方便与其他Spring框架进行搭配使用如：SpringCache