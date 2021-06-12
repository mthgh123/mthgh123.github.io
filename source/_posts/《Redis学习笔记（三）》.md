---
title: 《Redis学习笔记（三）》
date: 2021-02-27 14:55:15
categories:
- Redis
tags:
- Redis
---

![](http://qiniusave.codeyu.cn/redis.jpg)

<!--less-->

---

# 概述
本片主要是讲Redis进阶，这里才是真正实战且值钱的地方
## Redis配置文件详解（如何配置得更好一些）
启动的时候，就通过配置文件来启动的！

### 通用配置：
**单位：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194618189.png)配置文件unit单位对大小写不敏感


**包含：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194650634.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)
就好比我们学习Spring、Import、Include等语句把其他的部分包含进来

**网络：**
Bind 127.0.0.1   //绑定的IP
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194710730.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194715773.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)


**是否受保护的模式：**
Protected-mode yes   //保护模式
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194730919.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)


**端口：**
port 6379   //端口设置
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194740174.png)

**是否以守护进程开启：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194748628.png)

daemonize yes   //以守护进程的方式运行，默认是no，我们需要自己开启未yes!
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194757150.png)

 

注意：On Windows, daemonize and pidfile are not supported. windows系统中，daemonize and pidfile是不支持的，所以没有这两项配置项
<font color=red>日志级别：有debug、verbose、notice、warning，此处设定的是notice级别！</font>

**生成的日志文件的文件名：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194833676.png)

**默认的数据库数量：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194844884.png)



### 快照（SNAPSHOTTING）：与RDB持久化相关的配置
<font color=red>持久化：在规定的时间内，执行了多少次操作，则会持久化到文件（.rdb文件/.aof文件）</font>
快照下就三个方法：
它们表示的是一种持久化规则。<font color=red>在规定的时间内，执行了多少次操作，则会持久化到文件，即生成持久化文件！</font>
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194916163.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

**持久化发生错误时，是否仍然继续工作：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194926475.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

**是否压缩rdb文件：（rdb就是持久化的那个文件！） （如果开启的话需要消耗一些cpu资源）**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194939136.png)

**是否校验rdb文件：（如果rdb文件出错了，进行相应的修复！）**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194949860.png)

**持久化生成的目录：默认是当前目录**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301194957284.png)


### 与主从复制相关的配置：
后面讲解主从复制的时候再讲解！


### 安全相关的配置：
通过命令行在redis-cli.exe中设定密码：
config set requirepass “123456”
redis设定密码之后的登录方式：redis-cli.exe中键入命令：auth [密码]
如：auth 123456

### 客户端限制
**设置能连接上redis的最大客户端数量：默认10000个**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195042289.png)

**最大内存配置**：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195049433.png)

**内存到了最大内存之后的处理策略：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195058914.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

处理策略：比如移除一些过期的key
六种机制：
1、volatile-lru：只对设置了过期时间的key进行LRU（默认值） 

2、allkeys-lru： 删除lru算法的key   

3、volatile-random：随机删除即将过期key   

4、allkeys-random：随机删除   

5、volatile-ttl： 删除即将过期的   

6、noeviction： 永不过期，返回错误

### AOF的配置
**默认不开启AOF模式，因为默认是使用RDB方式进行持久化的：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195118687.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

在绝大多数情况下，RDB方式完全够用了！
**AOF持久化文件的名字：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195128928.png)

**同步开启相关配置：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195135556.png)

中间的是每秒执行一次同步。第一个是每次修改都会同步！（性能比较慢）
第三个是不执行同步操作！




## Redis持久化
面试和工作，持久化都是重点！
Redis是内存数据库，如果不将内存中的数据库状态保存到磁盘，那么一旦服务器进程退出，服务器中的数据状态也会消失。所以Redis提供了持久化功能！
<font color=red>关于RDB和AOF的原理，看它们对应的两幅原理图即可！</font>
### RDB（Reids DataBase）
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195159881.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的SnapShot快照，它恢复时是将快照文件直接读到内存里。

<font color=red>Redis会单独创建（fork）一个子进程进行持久化，会先将数据写入到一个临时RDB文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。</font>整个过程中，<font color=blue>主进程是不进行任何IO操作的。这就确保了极高的性能。</font>如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。***我们默认的就是RDB持久化方式，一般情况下不需要修改这个配置！***

**rdb保存的文件是dump.rdb**

<font color=red>触发生成rdb文件的机制：即触发持久化的机制：
1.save的规则满足的情况下，会自动触发rdb规则
2.执行flushall命令，也会触发我们的rdb规则！
3.退出redis，也会产生rdb文件！
备份就自动生成一个dump.rdb文件！</font>

<font color=red>如何恢复rdb文件中的数据：
只需要将rdb文件放在我们redis启动目录就可以，redis启动时就会自动检测dump.rdb恢复其中的数据。</font>

Rdb几乎就它自己默认的配置就够用了，但是我们还是需要去学习！

**优点：**
1.	适合大规模数据恢复
2.	对数据的完整性要求不高时也可使用

**缺点：**
1.需要一定的时间间隔进行持久化操作！如果reids意外宕机了，这个最后一次修改的数据就没有了！
2.fork进程的时候，会占用一定的内存空间！


### AOF（Append Only File）
将我们的所有命令都记录下来，history，恢复的时候就再把这个文件中记录的所有命令再执行一遍！
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195424931.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

以日志的形式来记录每个写操作，将Redis执行过的所有指令记录下来（读操作不记录），只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，<font color=red>redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作！</font>

<font color=red>Aof保存的是appendonly.aof文件</font>

**默认是不开启的，改为yes则开启：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195506728.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

如果aof文件有错误，这个时候redis是启动不起来的，我们需要修复这个aof文件。
Redis给我们提供了一个工具“reids-check-aof.exe”，使用它修复我们的aof文件之后即可启动redis了

**重写规则说明：**
Aof默认就是文件的无限追加，文件会越来越大！
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195521933.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

如果aof文件大于64m，太大了！redis就会frok一个新的进程来将我们的文件进行重写！


**优点：**
1.每一次修改都同步，文件的完整性会更好！
2.默认开启的是每秒同步一次，因此可能会丢失一秒的数据
3.从不同步，效率是最高的

**缺点：**
1.	相对于数据文件来说，aof远远大于rdb，修复的速度也比rdb慢！
2.	Aof运行效率也要比rdb慢，所以我们redis默认的配置就是rdb持久化！

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030119554030.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030119554594.png)

 

## Redis订阅与发布
Redis发布订阅（pub/sub）是一种<font color=red>消息通信模式</font>：发送者（pub）发送消息，订阅者（sub）接收消息。
Redis客户端可以订阅任意数量的频道。
订阅/发布消息图：可见[https://redisbook.readthedocs.io/en/latest/feature/pubsub.html](https://redisbook.readthedocs.io/en/latest/feature/pubsub.html)
第一个：消息发送者，第二个：频道，第三个：消息订阅者！
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195636100.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

下图展示了频道 channel1 ， 以及订阅这个频道的三个客户端 —— client2 、 client5 和 client1 之间的关系：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030119564377.png)

当有新消息通过 PUBLISH 命令发送给频道 channel1 时， 这个消息就会被发送给订阅它的三个客户端：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195647955.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030119565491.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

 


测试：
1.通过一个客户端订阅一个频道
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195700791.png)

2.通过另一个客户端来发布消息
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195715359.png)

3.消息发布之后，在订阅端即可收到消息：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195720617.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195734862.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)




**<font color=red>使用场景：</font>**
1.实时消息系统！
2.实时聊天（频道当作聊天室，将信息回显给所有人即可）
3.订阅，关注系统都是可以的
4.稍微复杂的场景：比如我们使用消息中间件MQ

## Redis主从复制
**概念：**
1主人---3仆从：比如一个主人带三个仆从，这也可以看做是一个小小的集群！
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301195827304.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

一般来说，要将redis运用于工程项目中，只使用一台Redis是万万不能的，原因如下：
1.从结构上讲，当个Redis服务器会发生单点故障，并且一台服务器需要处理所有的请求负载，压力较大。
2.从容量上讲，单个Redis服务器内存容量有限，就算一台Redis服务器内存容量为256G，也不能将所有内存用作Redis存储内存，一般来说，单台Redis最大使用内存不应该超过20G。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030119583985.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

<font color=red>**主从复制，读写分离！**</font>80%情况下都是在进行读操作！减缓服务器的压力！架构中经常使用！<font color=blue>当项目中存在主从复制时，那么至少搭建一个集群！---》搭建一个集群：最低配置为一主二从三台服务器！</font>
<font color=red>**只要在公司中，主从复制就是必须要使用的，因为在真实的项目中不可能单机使用Redis！**</font>

### 环境配置：
只配置从库，不用配置主库！（因为redis默认自己本身就是一个主库！）

查看当前库的信息：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301200050692.png)


Windows版本的Redis如何启动多个服务：即搭建集群环境：[https://blog.csdn.net/qq_44322555/article/details/104056258](https://blog.csdn.net/qq_44322555/article/details/104056258)

### 一主二从
默认情况下，每台Redis服务器都是主节点！我们一般情况下只用配置从机就好了！
配置从机的话，就直接认谁当老大即可：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301200146252.png)
命令：slaveof 【主机】 【端口号】

此处是通过命令配置的，但真实的主从配置是在配置文件中配置的，这样的话才是永久的配置，我们这里使用的是命令，是暂时的！

<font color=red>主机可以写，从机不能写只能读！主机中的所有信息和数据都会被从机保存！</font>

### 测试：
<font color=red>主机如果断开连接了：从机依旧是连接到主机的，但是没有写操作了。但如果主机又回来了，这个时候从机依旧可以直接获取到主机写的信息！</font>

<font color=red>从机如果断开连接了：那么从机会变成主机，但如果从机又连接上之前的主机，则主机中的数据都会同步到从机中去！</font>

### 复制原理
Slave启动成功连接到master后会发送一个sync命令
Master接收到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集的命令，在后台进程执行完毕之后，master将传送整个数据文件到slave，并完成一次完全同步！

<font color=red>**全量复制**：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。（简单说就是从机每次连接到主机时，会有一个同步操作，即从机将主机中的所有数据同步到从机中去）
**增量复制**：Master继续将新的所有收集到的修改命令依次传给slave，完成同步（简单说就是主机进行了写操作后，会立即将修改同步到从机中去）
但是只要是重新连接master，一次完全同步（全量复制）将被自动执行！</font>


### 宕机后手动配置主机
层层链路模型：上一个master连接下一个slave！
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301200323491.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

当处于第一个位置的主机正常工作时，后面的都是从机！并且能正常完成主从复制！

<font color=red>上面这两种模型在工作中**都不会使用**！</font>
在第二种模型中，当处于第一个位置的主机发生宕机时，在哨兵模式还没出来前，只能由手动配置第二个位置的从机变为主机！

命令：slaveof no one
当主节点断了的话，从机可以使用这个命令来使自己变为主节点！（<font color=red>**在哨兵模式没出来前，全得手动配置！**</font>）


## 哨兵模式（自动寻取老大的模式）（面试高频）
自动选取老大的模式！
#### 概述
主从切换技术的方法是：当主服务器宕机后，需要手动把一台从服务器换为主服务器，这就需要人工干预，费时费力，还会造成一段时间内服务不可用。这不是一种推荐的方式，更多的时候，我们优先考虑哨兵模式！Redis从2.8开始正式提供Sentinel（哨兵）架构来解决这个问题！

谋朝篡位的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库。

哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一种独立的进程，作为进程。其原理是哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis实例！

这里的哨兵有两个作用：
1.通过发送命令，让Redis服务器返回被监控服务器运行状态，包括主服务器和从服务器。
2.当哨兵监测到master宕机，会自动将slave切换成master，然后通过发布订阅模式通知其他的从服务器，修改配置文件，让它们切换主机。![](https://img-blog.csdnimg.cn/20210301200445809.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)
<font color=red>然而一个哨兵进程对Redis服务器进行监控，可能会出现问题，为此，我们可以使用多个哨兵进行监控。各个哨兵之间还会进行监控，这样就形成了多哨兵模式！</font>
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301200550859.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行**failover**过程，仅仅是哨兵1主观的认为主服务器不可用，这个现象为**主观下线**。当后面的哨兵也检测到主服务器不可用，并且数量到达一定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行failover【故障转移】操作。切换成功后，就会通过发布订阅模式，让各个哨兵把自己监控的从服务器实现切换主机，这个过程称为**客观下线**！

### 测试
1.配置哨兵配置文件sentinel.config
sentinel monitor myredis 127.0.0.1 6379 1
(监控127.0.0.1 6379这样的一个主机，端口号，1指的是投票1)


### 哨兵模式规则：
如果主机宕机，并且哨兵从从机中选出了新的主机后，曾经的主机又回来了，那么此时它只能归并到新的主机下，当作从机，这就是哨兵模式的规则！

#### 优点：
1.哨兵集群，基于主从复制模式，所有的主从配置优点，它全有！
2.主从可以切换，故障可以转移，系统的可用性会更好！
3.哨兵模式就是主从模式的升级，从手动到自动，更加健壮！
#### 缺点：
1.Redis不好在线扩容的，集群容量一旦到达上线，在线扩容就十分麻烦！
2.实现哨兵模式的配置其实是很麻烦的，里面有很多选择！


## Redis缓存穿透与雪崩（面试高频+工作常用）
<font color=red>Redis缓存的使用，极大的提升了应用程序的性能和效率，特别是数据查询方面。</font>但同时，**它也带来了一些问题**。其中，<font color=blue>**最要害的问题，就是数据的一致性问题**</font>，从严格意义上讲，这个问题无解。如果对数据的一致性要求很高，那么就不能使用缓存。另外的一些典型问题就是，缓存穿透、缓存雪崩和缓存击穿。目前，业界也都有比较流行的解决方案。

### 缓存穿透（查不到导致的）：
#### 概念
缓存穿透的概念很简单，用户想要查询一个数据，发现redis内存数据库没有，也就是缓存没有命中，于是向持久层数据库查询，发现也没有，于是本次查询失败。当用户很多的时候，缓存都没有命中，于是都去请求了持久层数据库。这会给持久层数据库造成很大的压力，这时候就相当于出现了缓存雪崩！

#### 解决方案：
**布隆式过滤器：** 
布隆过滤器是一种数据结构，对所有可能查询的参数以hash形式存储，在控制层先进行验证，不符合则丢弃（比如对服务器的攻击查询可能就被过滤并丢弃了），从而避免了对底层存储系统的查询压力；
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030120093166.png)

**缓存空对象：**
当存储层不命中后，即使返回的空对象也将其缓存起来，同时会设置一个过期时间，之后再访问这个数据将会从缓存中取出，保护了后端数据源；
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030120095262.png)

但是这种会存在两个问题：
1.如果空值能够被缓存起来，这就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有很多的空值的键；
2.即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会有影响！

### 缓存击穿（查的太多了导致的：量太大，缓存过期）：
#### 概念
这里需要注意和缓存穿透的区别。缓存击穿：是指一个key非常热点，在不停的扛着大并发，大并发集中对这一个点进行访问，当这个key在失效的瞬间，持续的大并发就会击穿缓存，直接请求数据库，就像在一个屏障上凿开了一个洞。

当某个key在过期的期间，有大量的请求并发访问，这类数据一般是热点数据，由于缓存过期，会同时访问数据库来查询最新数据，并且回写缓存，会导致数据库瞬间压力过大。

#### 解决方案：
**设置热点数据永不过期**
从缓存层面来看，没有设置过期时间，所以不会出现热点key过期后产生的问题。

**加锁互斥**
分布式锁：使用分布式锁，保证对于每个key同时只有一个线程去查询后端服务，其他线程没有获得分布式锁的权限，因此只需要等待即可。这种方式将高并发的压力转移到了分布式锁，因此对分布式锁的考验很大。

<font color=red>可以这样理解缓存穿透与缓存击穿的区别：穿透：缓存未命中，穿透到底层数据库。击穿：一点超时，大并发击穿到底层数据库。</font>

### 缓存雪崩：
#### 概念
缓存雪崩：是指在某一个时间段，缓存集中过期失效。

产生雪崩的原因之一，比如在写本文的时候，马上就要双十二零点，很快就会迎来一波抢购，这波商品时间比较集中的放入了缓存，假设只缓存了一个小时。那么到了凌晨一点钟的时候，这批商品的缓存就都过期了。而对这批商品的访问查询，都落到了数据库上，对于数据库而言，就会产生周期性的压力波峰。于是所有的请求都会达到存储层，存储层的调用量会暴增，造成存储层也会挂掉的情况。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301201038375.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70)

其实集中过期，倒不是非常致命，比较致命的缓存雪崩，是缓存服务器某个节点宕机或断网。因为自然形成的缓存雪崩，一定是在某个时间段集中创建缓存，这个时候，数据库也是可以顶住压力的。无非就是对数据库产生周期性的压力而已。而缓存服务节点的宕机，对数据库服务器造成的压力是不可预知的，很可能瞬间就把数据库压垮了。

#### 解决方案：
**Redis高可用**
这个思想的含义是，既然redis有可能挂掉，那我多增设几台redis，这样一台挂掉之后其他的还可以继续工作，其实就是搭建的集群。

**限流降级**
这个解决方案的思想是，在缓存失效后，通过加锁或者队列来控制都数据库写缓存的线程数量。比如对某个key只允许一个线程查询数据和写缓存，其他线程等待。

**数据预热**
数据加热的含义就是在正式部署之前，我先把可能的数据先预先访问一遍，这样部分可能大量访问的数据就会加载到缓存中。在即将发生大并发访问前手动触发加载缓存不同的Key，设置不同的过期时间，让缓存失效的时间点尽量均匀。