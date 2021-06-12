---
title: MySQL学习笔记（二）
date: 2021-03-10 19:28:53
categories:
- MySQL学习
tags:
- MySQL
---

![](http://qiniusave.codeyu.cn/MySQL.jpg)

<!--less-->

---

## 3.MySQL数据管理

### 3.1外键（了解即可---平时开发不用）

方式一：在创建表的时候，增加约束（麻烦，比较复杂）

删除有外键关系的表的时候，必须要先删除引用别人的表（从表），再删除被引用的表（主表）



方式二：创建表时没有外键关系，以单独的语句去定义外键关系

```sql
alter table `student` add constraint `FK_gradeid` foreign key(`gradeid`) reference `grade`(`gradeid`);
```

<font color='red'>以上操作都是物理外键，数据库级别的外键，我们不建议使用！（避免数据库过多造成的困扰！）</font>



<font color='red'>**最佳实践：**</font>

- 数据库就是单纯的表，只用来存数据，只有行（数据）和列（字段）
- 我们想使用多张表的数据，想使用外键（程序去实现！）

<font color='cornflowerblue'>阿里巴巴的JAVA规范中这样说：【强制】不得使用外键与级联，一切外键概念必须在应用层解决！原因是：每次做delete或者update都必须考虑外键约束，会导致开发的时候很痛苦，测试数据极为不方便！</font>



### 3.2DML语言（全部记住）

DML：数据操作语言

- insert
- update
- delete



### 3.3添加

```sql
insert into 表名([字段1,字段2,字段3,...]) values([值1,值2,值3,...]);

---------插入多个数据----------
insert into 表名([字段1,字段2,字段3,...])
values([值1,值2,值3,...]),([值1,值2,值3,...]),([值1,值2,值3,...])...;
```



### 3.4修改

```sql
update `表名` set `表中字段`=新的要改成的值 where 字段=某个值;   --如果不带where条件，则会把每一条数据的`表中字段`属性改为新的要改成的值

-------语法--------
update 表名 set column = value where [条件];
-------修改多个字段，使用`,`隔开--------
update 表名 set column1 = value1, column2 = value2... where [条件];
```



where的条件判断语句有：

大于（>）、小于（<）、等于（=）、不等于（!=）

还有：<font color='red'>between  A  and B</font>        如：where   id  between   3   and   5;   <font color='red'>（是闭合区间）</font>

<font color='red'>and</font>     如：where   id > 3 and id<5;

<font color='red'>or</font>   如：where   id = 3 or id = 5;



<font color='red'>注意：</font>

- colume   是数据库的列，尽量带上``
- 条件    筛选的条件，如果没有指定，则会修改所有的列
- value   是一个具体的值，也可以是一个变量

```sql
update `student` set `birthday` = current_time where `name`='长江7号' and sex='女';
```

### 3.5删除

#### delete命令

语法：`delete from 表名 [where 条件];`



#### truncate命令

作用：完全清空一个数据库表！表的结构和索引约束不会变！



#### delete命令与truncate命令区别

- 相同点：都能删除数据，都不会删除表结构
- 不同：
  - truncate：<font color='red'>重新设置，自增列/计数器会归零</font>
  - truncate：不会影响事务、
  - delete：<font color='red'>自增器不会归零，当再次往改表内插入值时，会在之前自增器到达的值的基础上继续往上增加，但truncate会使其归零</font>

 

## 4.DQL查询数据（最重点）

### 4.1DQL

（Data Query Language：数据查询语言）

```sql
---------查询某一个表的所有数据----------
select * from `表名`;

---------查询某一个表的某些字段的所有数据----------
select [字段1,字段2,字段3,...] from `表名`;

---------查询某一个表的id=2的某些字段的数据----------
select [字段1,字段2,字段3,...] from `表名` where id=2;

---------以别名的形式查询---------
select `studentNo` as `学号`, `studentName` as `学生姓名` from `student`;

---------函数：concat(a,b)拼接字符串---------
select concat('姓名：',studentName) from `student`;
```

语法：`select 字段,... from 表`



<font color='red'>注意：有时候，列名字不是那么的见名知意，我们可以起别名：as</font>



### 4.2指定查询字段

#### 去重（即有重复数据时去重）

```sql
---------去重查询----------
select distinct `StudentNo` from student;
```

<font color='red'>去重查询作用：去除`select`查询语句查询出的重复结果，只显示一条！</font>



#### 查询系统版本（函数）

```sql
select version();  --查询系统版本
```



#### 查询计算结果（表达式）

```sql
select 100*3-1;
select 100*3-1 as 计算结果;   --用来计算

---------学员成绩+1分查看---------
select `StudentNo`,`StudentReslut`+1 as '提分后' from reslut;
```



#### 查询自增的步长（变量）

```sql
select @@auto_increment_increment;   --查询自增的步长
```



#### 数据库中的表达式：

<font color='red'>文本值、列、null、函数、计算表达式、系统变量...</font>



### 4.3where条件子句

作用：检索数据中<font color='red'>符合条件</font>的值

#### 逻辑运算符

| 运算符                             | 语法                  | 描述   |
| ---------------------------------- | --------------------- | ------ |
| and    &&  （and或&&都可以）       | a and b       a&&b    | 逻辑与 |
| or     \|\|     （or或\|\|都可以） | a or b         a\|\|b | 逻辑或 |
| not     !                          | not  a         ! a    | 逻辑非 |

<font color='red'>在sql中尽量使用英语而少用符号！</font>



### 4.4模糊查询

模糊查询：本质是比较运算符

| 运算符        | 语法                    | 描述                              |
| ------------- | ----------------------- | --------------------------------- |
| is  null      | a  is  null             | 如果操作符为null，则结果为真      |
| is  not  null | a  is  not  null        | 如果操作符不为null，则结果为真    |
| between       | a  between  b  and  c   | 若a在b和c之间，则结果为真         |
| like          | a  like  b   （a 像 b） | SQL匹配：如果a匹配到b，则结果为真 |
| in            | a  in  (a1,a2,a3,...)   | a在一个元素集中，则结果为真       |



#### like的模糊查询

<font color='red'>**模糊查询：like 结合 %（代表0到任意个字符） 、 _（一个字符）**</font>

```sql
--比如使用模糊查询查出所有的姓刘的同学：
select `StudentNo`,`StudentName` from `student`
where StudentName like '刘%';

--比如使用模糊查询查出所有的姓刘的同学，且刘后面只有一个字的：
select `StudentNo`,`StudentName` from `student`
where StudentName like '刘_';

--比如使用模糊查询查出所有的姓刘的同学，且刘后面只有两个字的：
select `StudentNo`,`StudentName` from `student`
where StudentName like '刘__';

--模糊查询查询名字中间有“嘉”字的同学--
select `StudentNo`,`StudentName` from `student`
where StudentName like '%嘉%';   --可应对名字是2个字/3个字/4个字/n个字的情况
```



#### in的模糊查询

```sql
--查询学号为1001、1002、1003的学员
select `StudentNo`,`StudentName` from `student`
where StudentNo in (1001,1002,1003);  --in后面的（）括号内的值是具体的值，而不是像like一样的可以
```



#### 查询地址为null的学生

```sql
select `StudentNo`,`StudentName` from `student`
where address='' or address is null;
```



### 4.5联表查询

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308184213577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308184230417.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)



#### 联结查询的思路：

<font color='red'>1.分析需求，分析查询的字段来自哪些表</font>

<font color='red'>2.确定使用哪种联结查询（市面上有7种联结查询），确定交叉点，即这两个表中哪个数据是相同的！</font>

<font color='red'>比如：学生表中的StudnetNo = 成绩表中的StudnetNo，这样两个表就连起来了。</font>

<font color='red'>3.假设存在一种多张表的联结查询，慢慢来，先查询两张表，然后再慢慢增加！</font>

```sql
--现在有两张表：学生表和成绩表。学生表（student）中有字段StudnetNo和StudnetName，成绩表（result）中有字段StudnetNo和SubjectNo、ExamDate、StudentResult。

--按照上述规则，需要查询：StudnetNo、StudnetName、SubjectNo、StudentResult这些数据，请写出相应的sql语句？
select s.StudnetNo、StudnetName、SubjectNo、StudentResult --按照规则1知道要查这些字段
from student as s inner join result as r --确定要从哪些表中查到这些数据以及确定联结关系
where s.StudnetNo=r.StudnetNo;   --此处也可用on s.StudnetNo=r.StudnetNo;


select s.StudnetNo、StudnetName、SubjectNo、StudentResult
from student as s right join result as r --此处使用right join，是将result作为右表，且以其为基准
on s.StudnetNo=r.StudnetNo;

select s.StudnetNo、StudnetName、SubjectNo、StudentResult
from student as s left join result as r --此处使用left join，是将student作为左表，且以其为基准
on s.StudnetNo=r.StudnetNo;

---查询缺考的同学---
select s.StudnetNo、StudnetName、SubjectNo、StudentResult
from student as s left join result as r --此处使用left join，是将student作为左表
on s.StudnetNo=r.StudnetNo;
where StudentResult is null;
```



| 操作        | 描述                                       |
| ----------- | ------------------------------------------ |
| inner join  | 如果表中至少有一个匹配，就返回行           |
| left  join  | 会从左表中返回所有值，即使右表中没有匹配的 |
| right  join | 会从右表中返回所有值，即使左表中没有匹配的 |



#### <font color='red'>**join on和where的区别：**</font>

join on 连接查询 ：join（连接的表）on（判断条件）

where 等值查询：等值查询



where只能在Inner join中用吗？能否在left join / right join 中使用！



#### left join和right join是以谁为基准的

```sql
from student as s left join result as r
```

<font color='red'>如果是left join，则以此时在左边的表为基准！即student表为基准！</font>

```sql
from student as s right join result as r
```

<font color='red'>如果是right join，则以此时在右边的表为基准！即result表为基准！</font>



#### 思考题：联表查询student、result、subject

```sql
----查询出studentNo、studentName、subjectName、studentReslut--
--studentNo、studentName属于student表，subjectName属于subject表，studentReslut属于result表，则查询语句为：
select s.studentNo,studentName,subjectName,StudentResult
from student as s
right join result as r
on r.studentNo=s.studentNo
inner join subject as sub
on r.subjectNo=sub.subjectNo     --subject表和result表都有subjectNo字段
```



#### 自连接

所谓自连接就是自己的表和自己的表连接，核心：一张表拆为两张一样的表即可。



#### select查询语句语法

记住下图的各种语句所放置的位置：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210308184357548.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




### 4.6分页和排序

分页和排序分别对应关键字：limit 和 order by



#### 1.排序：升序 Asc  /  降序 Desc

```sql
order by `字段名` asc;   --升序排序
order by `字段名` desc;   --降序排序
```



#### 2.分页

为什么要分页？

缓解数据库压力。

<font color='red'>**与分页思路类似的也有“瀑布流”操作！**</font>比如百度图片，当往下滑动时，不断刷新出新的数据！

**语法：**

```sql
-- limit 后有两个参数：查询起始值，查询数据的条数（一般和PageSize对应）
```

对于分页的理解：

第一页：limit  0,5

第二页：limit  5,5

第三页：limit  10,5

第n页：limit  (n-1)*5,5

所以：总页数 =（总数据条数）/ PageSize + 1

总数据条数通过查询语句获得； PageSize由前端返回给后端

实际上：当数据量不大时，分页可以由前端来做，每次查询数据库时，将所有数据丢给前端即可，而当数据量大时，分页由后端来实现则更好！



### 4.7子查询（更推荐使用联表查询）

我们之前使用where判断语句时，where后的表达式中的值都是固定的，比如：

where  id = 20;

where  id > 20 and id < 40;

<font color='red'>**而所谓的子查询：where后的判断语句不是固定的，而是计算出来的！**</font>

<font color='red'>**本质：在where语句中嵌套一个子查询语句**</font>

如：where  (select  *  from)

看下方的试题：

```sql
--查询 数据库结构-1 的所有考试结果（学号、科目编号、成绩）并降序排列
--方式一：使用连接查询
select `StudentNo`, r.`SubjectNo`, `StudentResult`
from `result` r
inner join `subject` sub
on r.SubjectNo = sub.SubjectNo
where SubjectName = '数据库机构-1'
order by StudentResult desc;

--方式二：使用子查询
select `StudentNo`, `SubjectNo`, `StudentResult`
from `result`
where SubjectNo = (
	select SubjectNo from `subject`
    where SubjectName = '数据库机构-1'         --执行顺序为由里及外
)
order by StudentResult desc;
```



## 5.MySQL常用函数

常用函数官网：https://dev.mysql.com/doc/refman/8.0/en/sql-function-reference.html



### 5.1常用函数（也并不常用）

```sql
--------数学运算--------
select ABS(-8)	--绝对值
select ceiling(9.1)	--向上取整
select floor(9.1)	--向下取整
select rand()	--返回一个0-1之间的随机数

--------字符串函数--------
select char_length('123456')	--返回字符串长度
select concat('123456','789')	--拼接字符串
select insert()	--在指定位置插入字符串
select lower()	--转小写
select upper()	--转大写
select replace()	--替换字符串

--------时间和日期--------
select current_date() 或者 select current_date	--获取当前日期
select curdate()	--获取当前日期
select now()	--获取当前日期和时间
select sysdate()	--获取系统当前日期和时间
select timestamp() 或者 select timestamp	--获取当前日期和时间
select localtime() 或者 select localtime  --获取当前日期和时间
select localtimestamp() 或者 select localtimestamp --获取当前日期和时间

--------年/月/日/时/分/秒--------
select year(now())	--返回当前时间对应年
select month(now())	--返回当前时间对应年
select day(now())	--返回当前时间对应年
select hour(now())	--返回当前时间对应年
select minute(now())	--返回当前时间对应年
select second(now())	--返回当前时间对应年

--------系统--------
select system_user()	--系统当前用户
select user()	--当前用户
select version()	--当前mysql版本
```



### 5.2聚合函数（更常用）

```sql
count() --计数（重要） select count(*) from `student`; 返回student表中数据的条数
sum()	--求和
avg()	--平均值
max()	--最大值
min()	--最小值
```



#### count()函数

```sql
--三种能做统计的count()方法
select count(studentName) from student;	-- count(字段) -> 返回student表中数据的条数
select count(*) from student; --返回student表中数据的条数
select count(1) from student; --返回student表中数据的条数
--上述三者的区别：
/*
count(字段)会忽略所有null值
count(*) 和 count(1)不会忽略null值
*/
```



#### 分组过滤：

```sql
select SubjectName, avg(StudentResult) as 平均分, max(StudentResult), min(StudentResult)
from result r
inner join `subject` sub
on r.`SubjectNo` = sub.`SubjectNo`
group by r.SubjectNo
having 平均分>80; --因为上面给avg(StudentResult)起了别名为平均分，所以此处可以使用平均分，注意：此处不能使用where进行判断，因为是对结果进行再过滤，需要使用having
```



### 5.3数据库级别的MD5加密（扩展）

什么是MD5？

MD5由MD4、MD3、MD2改进而来，主要增强算法复杂度和不可逆性！

**使用MD5给数据库中存入的用户密码加密：**

```sql
--创建表
CREATE TABLE `testmd5` (
  `id` int NOT NULL,
  `name` varchar(20) NOT NULL,
  `pwd` varchar(50) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8

--插入数据
insert into `testmd5` values (1,'zhangsan','123456'), (2,'lisi','123456');

--加密
update testmd5 set `pwd`=MD5(`pwd`) where id=1;
```

但上述这种操作是密码已经设置为123456后再给他加密的，正常情况下应该是设置的时候进行加密的！如下：

```sql
--插入数据时加密
insert into `testmd5` values (4,'xiaoming',MD5('123456')); --MD5()进行加密
```

#### 如何校验

<font color='red'>将从前端传进来的用户密码进行md5加密，然后再与数据库中md5加密后的密码进行对比，相同则说明密码正确！</font>

那么可以这样对比校验：

```sql
select * from testmd5 where `name`='xiaoming' and `pwd`=MD5('123456'); --此处的'123456'即为用户传进来的密码
```



#### 更复杂一些的业务层面的数据查询

查询：跨表、跨数据库（比如：在mysql、oracle不同数据库之间查询）

比如：淘宝上某一个商品的详细信息，包括商品的图片、视频、评论等等，一般而言，图片、视频来自不同的数据库，所以这也就是<font color='red'>跨数据库查询</font>！并将结果显示在一个页面中！