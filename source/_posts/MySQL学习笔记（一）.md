---
title: MySQL学习笔记（一）
date: 2021-03-08 10:03:40
categories:
- MySQL学习
tags:
- MySQL
---

![](http://qiniusave.codeyu.cn/MySQL.jpg)

<!--less-->

---

## 1.初识MySQL

JavaEE：企业级Java开发 Web

前端（页面：展示，数据！）

后台（连接点：连接数据库JDBC，连接前端（控制，控制视图跳转，和给前端转递数据））

数据库（存数据）

### 1.1为什么学数据库

1.岗位需求

2.现在的世界，大数据时代~

<font color='red'>3.数据库是所有软件体系中最核心的存在！</font>



### 1.2什么是数据库

数据库（DB：DataBase）

概念：数据仓库（<font color='red'>是一款软件</font>：安装在操作系统（windows、linux、mac...）之上！）SQL：可以存大量数据，500万！

作用：存储数据，管理数据！



### 1.3 DBMS

**关系型数据库：类似于Excel样的行和列组成** （SQL）

- MySQL、Oracle、SqlServer、DB2
- 通过表和表之间，行和列之间的关系进行数据的存储。如：学员信息表、考勤表...



**非关系型数据库：{key：value}  (NoSQL)**

- Redis、MongoDB
- 非关系型数据库：对象存储，通过对象的自身属性来决定！



<font color='red'>**DBMS（数据库管理系统）**</font>

- 数据库的管理软件，它可以科学有效的管理我们的数据。维护和获取数据。
- <font color='red'>MySQL：本质是数据库管理系统！</font>数据也是存入其中



### 1.4MySQL简介

MySQL是一个<font color='red'>关系型数据库管理系统给</font>

开源的数据库软件

体积小、速度快、总体拥有成本低

适用于中小型网站、或者大型网站（集群！）

官网：https://www.mysql.com/



### 1.5安装MySQL

1.解压

2.在环境变量中加入mysql解压后的bin目录路径

3.配置环境变量

4.新建mysql配置文件.ini

```ini
[mysqld]

# 设置端口3306（mysql的默认端口为3306）

port=3306

# 设置mysql的安装目录

basedir=D:\MySQL\mysql-8.0.19-winx64

# 设置mysql数据库的数据的存放目录

datadir=D:\MySQL\mysql-8.0.19-winx64\data

# 允许最大连接数

max_connections=200

# 允许连接失败的次数。

max_connect_errors=10

# 设置mysql服务器使用的字符集，默认为UTF8

character-set-server=utf8

# 创建新表时将使用的默认存储引擎

default-storage-engine=INNODB

[mysql]

# 设置mysql客户端默认字符集

default-character-set=utf8

[client]

# 设置mysql客户端连接服务端时默认使用的端口

port=3306

default-character-set=utf8
```

5.启动管理员模式下的CMD，并切换到mysql下的bin目录，然后输入<font color='red'>mysqld -install</font> （安装mysql）

6.在输入`mysql --initialize-insecure --user=mysql` 初始化数据文件

7.然后再次打开cmd，并输入`net start mysql` 以<font color='red'>启动mysql服务</font>，然后输入 `mysql -u root -p` 进入mysql管理界面（密码可为空）

8.修改密码：

```sql
--执行这个sql语句以修改密码--
update mysql.user set authentication string=password('123456') where user='root' and Host='localhost';
```

之后输入：`flush privileges;`

9.重启mysql，即执行：`net stop mysql` 命令和 `net start mysql` 命令



### 1.6连接数据库

命令行：

```sql
-------------连接数据库--------------
mysql -u root -p
-------------更新数据库密码--------------
update mysql.user set authentication string=password('123456') where user='root' and Host='localhost';
-------------刷新权限--------------
flush privileges;
-------------查看所有数据库--------------
show database;
-------------使用某一个数据库（切换数据库）--------------
use (数据库名);
-------------查看所使用数据库中的所有表--------------
show tables;
-------------显示数据库中某一个表的详细信息--------------
describe (表名);
-------------创建一个数据库--------------
create database (数据库名字);
-------------退出MySQL连接--------------
exit;

```



DDL：数据库定义语言

DML：数据库操作语言

DQL：数据库查询语言

DCL：数据库控制语言



## 2.操作数据库

操作数据库》操作数据库中的表》操作数据库中表的数据

<font color='red'>mysql不区分大小写</font>

### 2.1操作数据库（了解即可）

1.创建数据库

```sql
--此处的中括号中[if not exists]命令指的是可选的
create database [if not exists] `chichi`;
```

2.删除数据库

```sql
drop database [if exists] `chichi`;
```

3.使用数据库

```sql
use `chichi`;
```

4.查看数据库

```sql
--查看所有的数据库
show databases;
```



### 2.2数据库的列的数据类型

#### 数值

- tinyint	     十分小的数据	1个字节
- smallint      较小的数据     2个字节
- mediumint      中等大小的数据    3个字节
- **int                 标准的整数          4个字节（常用的，与java中的int类型对应）**

注意：int(11)指的是什么？答：它不是指能存储的数据的最大位数是11位，而是指显示的宽度是11位。就相当于int(3)，它的<font color='red'>显示位宽</font>为3，它是int类型的，在mysql中最大值2^32-1（4个字节），而不是说它只能显示三位大小的数！

- bigint            较大的整数          8个字节（与java中的long类型对应）
- float              浮点数                  4个字节
- double         浮点数                  8个字节
- decimal       字符串形式的浮点是      金融计算的时候，一般使用decimal

#### 字符串

- char                    字符串是固定大小      0-255
- **varchar             可变字符串        0-65535    （常用的：与java中的String对应）**

<font color='red'>注意：而varchar(20)指的则是只能最多存储20位的字符，与int(20)不同</font>

- tinytext             微型文本          2^8-1
- text                   文本串              2^16-1        （保存大文本）

#### 时间日期

java中的时间日期类为：`java.util.Data`

- date             YYYY-MM-DD       日期格式
- time             HH：mm：ss       时间格式
- **datetime     YYYY-MM-DD  HH：mm：ss       最常用的时间格式**
- **timestamp    时间戳（1970.01.01到现在的毫秒数！）      也较为常用**
- year      年份表示

#### null

- 没有值，未知
- <font color='red'>注意：不要使用null进行运算，结果为null</font>



#### COLLATE utf8_general_ci的含义

指地是校验规则为：utf8_general_ci

ci是：case insensitive的缩写，指大小写不敏感





### 2.3数据库的字段属性（重点）

<font color='red'>Unsigned：</font>

- 无符号整数
- 声明了该列不能为负数



<font color='red'>zreofill：</font>

- 0填充的（假设写一个长度为10的int类型int(10)的值为1，那么会自动用0填充成长度为0000000001）
- 即不足的位数，使用0来填充       如：int(3)      那么5会变为005



<font color='red'>自增：</font>

-  自动在上一条记录的主键值基础上+1
-  通常用来设计唯一的主键（必须是整数类型）
-  也可自定义设计主键自增的起始值和步长



<font color='red'>非空 null和not null：</font>

- 假设设置为not null，如果不给它赋值，就会报错！
- null，如果不填写，默认就是null！



<font color='red'>默认（default）：</font>

- 设置默认值



<font color='red'>id</font>

- 自增主键



**每一个表都必须包含以下五个字段！未来做项目用的，表示一个记录存在的意义！**

<font color='red'>id  主键</font>

<font color='red'>version（即每一条记录都有自己对应的一个版本）</font>这个东西可以用来做乐观锁！

<font color='red'>is_delete：</font>伪删除

<font color='red'>gmt_create    创建时间</font>

<font color='red'>gmt_update   修改时间</font>



### 2.4创建数据库表(重点)

```sql
create table if not exists `student`(
    `student_id` int(11) not null AUTO_INCREMENT comment '学号',
    `password` varchar(20) character set utf8 collate utf8_general_ci not null default '0000' COMMENT '密码',
    `name` varchar(10) character set utf8 collate utf8_general_ci not null default "张三" COMMENT '姓名',
    `gender` varchar(2) character set utf8 collate utf8_general_ci not null default "男" COMMENT '性别',
    `birthday` TIMESTAMP default null COMMENT '出生日期',
    `address` varchar(50) character set utf8 collate utf8_general_ci not null COMMENT '地址',
    `email` varchar(50) character set utf8 collate utf8_general_ci not null COMMENT '邮件',
		primary key (`student_id`) using BTREE
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARACTER set = utf8 collate = utf8_general_ci;
```

#### 建表格式：

```sql
create table [if not exists] `表名` (
	`字段名1` 列数据类型 [属性] [索引] [注释],
    `字段名2` 列数据类型 [属性] [索引] [注释],
    ······
    `字段名n` 列数据类型 [属性] [索引] [注释],
    primary key (`字段名x`) using btree      --最后一行不加`,`号
)	[ENGINE=InnoDB] [default character set=utf8] [collate=utf8-general-ci;] --这几个属性也是可选的，可不写
```

#### 查看创建数据库/表的sql语句

```sql
show create database `数据库名`;   --查看创建数据库的语句
show create table `数据库中的表名`;   --查看创建数据库中的表的语句
desc `数据库中的表名`;   --显示表结构
```



### 2.5数据表的类型

InnoDB：现在默认使用的

MyISAM：早些年使用的

|                              | MyISAM                     | InnoDB                 |
| ---------------------------- | -------------------------- | :--------------------- |
| 事务支持                     | 不支持（现在新版的则支持） | 支持                   |
| 数据行锁定                   | 不支持（因为MyISAM是表锁） | 支持（InnoDB是行锁）   |
| 外键约束                     | 不支持                     | 支持                   |
| 全文索引（应用：如百度搜索） | 支持                       | 不支持                 |
| 表空间大小                   | 较小                       | 较大（约为MyISAM两倍） |

**常规使用操作：**

- MyISAM      节约空间，速度较快
- InnoDB       安全性高，支持事务，多表多用户操作



#### 在屋里空间存在的位置

所有的数据库文件都存在data目录下

本质还是文件的存储！

<font color='red'>MySQL在data目录下数据库中对应的表全是.ibd文件！</font>



#### 设置数据库表的字符集

```sql
character set = uts8
```

不设置的话，会使用mysql默认的字符集编码~（不支持中文！）

在my.ini中配置默认的编码！



### 2.6修改和删除表

#### 修改

```sql
alter table `表名` rename as `表名1`;    --修改表的名字

alter table `表名` add `字段名` [字段属性];    --增加表的字段，如：alter table `student` add `age` int(11);


----------下方为修改表的字段-----------
--1.修改约束(modify)
alter table `表名` modify `字段名` 字段属性;
--如：alter table `student` modify `age` varchar(11);   --即将int(11)类型的age字段改为varchar(11)类型
--modify能修改字段类型和约束，不能用来对字段重命名！

--2.字段重命名(change)
alter table `表名` change `旧字段名` `新字段名` 新字段属性;
--如：alter table `student` change `age` `age1` int(1);
--change用来字段重命名，不能修改字段类型和约束！
```



#### 删除

```sql
----------删除表的字段-----------
alter table `表名` drop `要被删除的字段名`;


----------删除表-----------
drop table if exists `表名`;
```

<font color='red'>**所以的创建和删除操作尽量加上判断，以免报错！**</font>



#### 注意点：

- ``   字段名，使用这个符号包裹
- 注释：-- 或者 /**/
- sql关键字大小写不敏感，建议使用小写