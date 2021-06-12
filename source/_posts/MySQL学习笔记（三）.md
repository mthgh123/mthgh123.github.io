---
title: MySQL学习笔记（三）
date: 2021-03-12 17:55:57
categories:
- MySQL学习
tags:
- MySQL
---

![](http://qiniusave.codeyu.cn/MySQL.jpg)

<!--less-->

---

## 6.事务

### 6.1什么是事务

<font color='red'>要么都成功，要么都失败！</font>即将一组SQL放在一个批次中去执行，都成功了则算作成功，然后持久化到数据库，若其中有一个失败了则算作失败，并回滚以恢复到原状！

#### 事务原则：ACID属性

<font color='red'>原子性</font>：要么都成功、要么都失败

<font color='red'>一致性</font>：事务前后数据的完整性要一致

<font color='red'>隔离性</font>：事务的隔离性是多个用户并发访问数据库时，数据库为每一个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离！

<font color='red'>持久性</font>：事务一旦提交则不可逆，被持久化到数据库中



#### 事务的隔离性又存在相应的隔离级别：读未提交、读已提交、可重复读、序列化

读未提交（read_uncommitted）：存在脏读、幻读、不可重复读问题

读已提交（read_committed）：解决脏读问题，但存在幻读、不可重复读问题

可重复读（repeatable_read）：解决脏读、幻读问题，但存在不可重复读问题

序列化（serializable）：解决脏读、幻读、不可重复读问题，但效率比上述的隔离级别更低！

#### 不同的隔离级别又会产生相应的问题：脏读、幻读、不可重复读

 加深理解可见该博客：https://blog.csdn.net/dengjili/article/details/82468576



### 6.2执行事务

mysql是默认开启事务自动提交的！即每写一个insert into语句数据直接就被插入到数据库了！

```sql
--关闭mysql的事务自动提交操作：
set autocommit = 0 /*关闭*/
set autocommit = 1 /*开启*/
```

<font color='red'>**当事务自动提交处于关闭状态时，如果更新数据库的数据，但事务没有提交的话，数据库中的值是不会变化的，直到事务提交后才会变化！**</font>

#### 手动处理事务

```sql
--手动处理事务
set autocommit = 0  --关闭自动提交
--事务开启
start transaction   --标记事务的开始，从这之后的sql都在同一个事务内！

--事务内的操作
insert xxx
insert xxx

--提交事务：持久化到数据库(成功则提交！)
commit
--回滚事务：回到原来的样子(失败则回滚！)
rollback
--事务结束之后
set autocommit = 1  --开启自动提交

--另外可以了解一下的内容--
savepoint  --设置一个事务的保存点
rollback to savepoint  --回滚到保存点
release savepoint	--撤销保存点
```



#### 模拟场景

```sql
--创建表
CREATE TABLE `account` (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(40) NOT NULL DEFAULT '',
  `money` float NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8

--插入数据
insert into `account` values(1,'A',800),(2,'B',1200),(3,'C',1000);

-- 模拟转账：事务
set autocommit = 0; --关闭自动提交
--------------------------
start transaction   --开启一个事务
update account set money=money-500 where `name`='A'   --A减500
update account set money=money+500 where `name`='B'   --B加500

commit;   --提交事务
rollback;   --回滚

--------------------------
set autocommit = 1;   --恢复默认值
```



## 7.索引

介绍索引的写的非常好的一篇博客！http://blog.codinglabs.org/articles/theory-of-mysql-index.html

也可以看看这个，介绍索引的实现：https://www.cnblogs.com/bonelee/p/6225211.html

<font color='blue'>MySQL官方对索引的定义为：**索引（Index）是帮助MySQL高效获取数据的数据结构**。提取句子主干，就可以得到索引的本质：索引是数据结构。</font>

### 7.1索引的分类

- 主键索引：即建表时的primary key
  - 唯一的标识，主键索引不可以有多个，只能有一个列作为主键
- 唯一索引：unique key
  - 一个表中唯一索引可以有多个，多个列都可以标识为唯一索引
- 普通索引：key 或 index
  - 默认的，可以用key/index关键字进行设置
- 全文索引：FullText
  - 快速定位数据

<font color='red'>在一个表中，主键索引只能有一个，唯一索引可以有多个！</font>

#### 索引的创建：

<font color='red'>1.在创建表的时候给字段增加索引</font>

<font color='red'>2.创建完毕后，增加索引</font>

```sql
--显示所有的索引信息
show index from `表名`;

--给已经建好的表创建一个索引
alter table `表名` add [unique | key | FullText] index --此处分别为唯一/普通/全文索引
`索引名` (`列名`);   -- `索引名` (`列名`)
--如：
/*
alter table `account` add unique index `Index_money` (`money`);  --索引名可随意取
*/
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308184559449.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


#### 创建索引的另一种方式：

```sql
create 索引类型 `索引名` on `表名` (`字段`);
```

#### 创建索引的三方式：

```sql
--方式一：在创建表的时候顺便创建索引


--方式二：
alter table `表名` add [unique | key | FullText] index --此处分别为唯一/普通/全文索引
`索引名` (`列名`);

--方式三：
create 索引类型 index `索引名` on `表名` (`表中字段`); --此处的索引类型有唯一/普通/全文索引等
```

#### 删除索引：

```sql
drop index 索引名 on 表名;
```



#### mysql优化--看懂explain

可以看博客：https://blog.csdn.net/jiadajing267/article/details/81269067



#### explain--分析sql语句/分析sql执行的状况

```sql
explain select * from student;
```



#### 非全文索引

<font color='red'>当数据量少时，不需要使用全文索引，可以使用match进行匹配操作！但数据量太少可能match匹配也不起作用！</font>

```sql
select * from `student` where match(studentName) against('刘');
```



### 7.2测试索引

<font color='red'>百万级数据时，在没有索引的情况下，查询一条数据平均0.8s左右，建立索引之后，查询一条数据稳定在0.001s左右！</font>

索引在小数据量的时候，用处不大，但是在大数据量的时候，区别十分明显！

但维护索引需要开销，导致插入数据时，速度还会变慢，不过好在大部分情况下插入数据的情况不多，而读数据的情况比较多！



### 7.3索引原则

- 索引不是越多越好
- 不要对经常变动的数据加索引
- 小数据量的表不需要加索引
- 索引一般加在常用来查询的字段上（以加快查询速度）



#### 索引的数据结构

Hash类型的索引

Btree：默认类型索引



阅读：http://blog.codinglabs.org/articles/theory-of-mysql-index.html



<font color='blue'>我们此处学习的是业务级别的mysql，主要包括：我们上面所学的这些内容</font>

<font color='blue'>还有运维级别的mysql，主要包括：mysql的底层、mysql架构原理、mysql集群搭建、mysql的负载均衡等、mysql主从复制等，这是业务级别的mysql中所讲不到的！有兴趣的话也可以去学习以下运维级别的mysql</font>



## 8.权限管理和备份

### 8.1用户管理

#### SQL命令操作

用户表：mysql.user   （即mysql这个数据库下的user表）

<font color='red'>所以权限管理实际上就是对mysql.user 这张表进行增删改查！</font>



### 8.2MySQL备份

为什么要备份：

- 保证重要的数据不丢失！
- 数据转移 A--->B



MySQL数据库备份的方式

- 直接拷贝物理文件
- 使用可视化工具中手动导出数据库
- 使用命令导出：mysqldump命令

```sql
--使用mysqldump命令
--mysqldump  -h 主机  -u 用户名  -p 密码  数据库  表名1 表名2 ...  >  物理磁盘位置/文件名
mysqldump -hlocalhost -uroot -p123456 school student > D:/backup.sql;
```

导入.sql文件：

```sql
--source命令，导入.sql文件
source 物理磁盘位置/文件名;
--mysql -u 用户名 -p 密码 库名 < 备份文件
```



## 9.规范数据库设计

###  9.1为什么需要设计数据库

<font color='red'>当数据库比较复杂的时候，我们就需要设计了！</font>

#### 糟糕的数据库设计：

- 数据冗余，浪费空间
- 数据库插入和删除都很麻烦、异常（比如使用了物理外键）
- 程序的性能差

#### 良好的数据库设计：

- 节省内存空间
- 保证数据库的完整性
- 方便我们开发系统

#### <font color='red'>软件开发中，关于数据库的设计：</font>

- 分析需求：分析业务和数据库所需要满足的需求
- 概要设计：设计关系图 E-R 图



#### 设计数据库的步骤：（以个人博客为案例）

- 收集信息，分析需求
  - 用户表（用户登录注销，用户的个人信息，写博客，创建分类）
  - 分类表（文章分类，谁创建的）
  - 文章表（文章的信息）
  - 友联表（友链信息）
  - 自定义表（系统信息，某个关键字，或者一些主字段） key：value
- 标识实体（把需求落实到每个字段）
- 标识实体之间的关系
  - 写博客：user--->blog
  - 创建分类：user--->category
  - 关注：user--->user
  - 友链：links
  - 评论：user--->user--->blog



### 9.2三大范式

为什么需要数据规范化？

- 信息重复
- 更新异常
- 插入异常
  - 无法正常显示信息
- 删除异常
  - 丢失有效的信息



#### 三大范式：

可参考：https://www.cnblogs.com/wsg25/p/9615100.html

**第一范式（1NF）**

<font color='red'>**原子性：保证每列不可再分**</font>



**第二范式（2NF）**

前提：满足第一范式

<font color='red'>**每张表只描述一件事情！**</font>



**第三范式（3NF）**

前提：满足第一和第二范式

<font color='red'>**第三范式需要确保数据表中的每一列数据都和主键直接相关，而不能间接相关！**</font>



#### 规范性和性能问题

关联查询的表不得超过三张表

- 考虑商业化的需求和目标（成本，用户体验等！）数据库的性能更加重要
- 在规范性能的问题的时候，需要适当的考虑以下规范性！
- 故意给某些表增加一些冗余的字段！（从多表查询变为单表查询）
- 故意增加一些计算列（从大数据量降低为小数据量的查询：查询）