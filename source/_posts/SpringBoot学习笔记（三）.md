---
title: SpringBoot学习笔记（三）
date: 2021-02-07 15:08:12
categories:
- SpringBoot学习
tags:
- SpringBoot
---

![](http://qiniusave.codeyu.cn/springboot.jpg)

<!--less-->

---

## 学习任务

- JDBC
- **Mybatis：重点**
- **Druid：重点**
- **Shrio：重点**
- **Spring Security：安全：重点**
- 异步任务~，邮件发送，定时任务
- Swagger：前后端交接的文档技术
- Dubbo+Zookeeper



## 整合JDBC使用

对于数据访问层，无论是SQL（关系型数据库）还是NOSQL（非关系型数据库），Spring Boot底层都是采用Spring Data的方式进行统一处理。
Spring Boot 底层都是采用Spring Data的方式进行统一处理各种数据库，Spring Data也是Spring中与Spring Boot、Spring Cloud等齐名的知名项目。
Sping Data 官网：https://spring.io/projects/spring-data

数据库相关的启动器：可以参考官方文档：https://docs.spring.io/spring-boot/docs/2.1.7.RELEASE/reference/htmlsingle/#using-boot-starter



### SpringData可以交互的组件

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301161034113.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


和springData相关的pom依赖：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030116105572.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




### SpringBoot使用JDBC能有多方便

分2步：

1.在yaml/properties配置文件中配置基本属性

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301161115434.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


2.通过@AutoWired注解注入Bean即可使用了

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301161132988.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

### JDBC与JdbcTemplate的区别

JDBC（Java DataBase Connectivity,java数据库连接）是一种用于执行SQL语句的Java API，

可以为多种关系数据库提供统一访问，它由一组用Java语言编写的类和接口组成。

而多的这个template,就是模板,是Spring框架为我们提供的.

所以JDBCTemplate就是Spring对JDBC的封装,通俗点说就是Spring对jdbc的封装的模板



### JDBC与Mybatis的区别

原生的Jdbc需要自己在业务代码间写sql语句，而不像Mybatis那样



## Druid DataSource 

DRUID简介：
Druid 是<font color='blue'>阿里巴巴</font>开源平台上一个数据库连接池实现，结合了C3PO、DBCP、PROXOOL等DB池的优点，同时加入了日志监控。
Druid 可以很好的监控DB池连接和SQL的执行情况，天生就是针对监控而生的DB连接池。
Spring Boot 2.0以上默认使用Hikari数据源，可以说Hikari与Driud都是当前Java Web上最优秀的数据源，我们来重点介绍Spring Boot 如何集成Druid 数据源，如何实现数据库监控。

<font color='orange'>Hikari数据源的优点：HikariDataSource 号称Java WEB当前速度最快的数据源，相比于传统的C3PO、DBCP、Tomcat</font>



### 如何使用Druid数据源

分2步：

1.引入数据源依赖

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.4</version>
</dependency>

```

2.在配置文件中加入数据源类型

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301161237270.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


测试结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301161255766.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

### 关于durid可配置的内容还有这么一些：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301161317207.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

### druid真正强大的地方在于自定义一些配置：

新建一个配置类，<font color='red'>将该配置类中的方法与yaml配置文件绑定起来，通过@ConfigurationProperties实现配置类方法与配置文件的绑定</font>！（在之前讲解的通过配置文件给类赋初始值时讲过，不过那时是将@ConfigurationProperties注解标注在类上面！）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301161345450.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

### Druid后台监控

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301161405401.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




## 整合Mybatis框架

分几步：

1.加入依赖项

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
```

<font color='red'>因为pom.xml文件中的父依赖中没有mybatis的相关配置项，所以此处应该指明mybatis的版本才行！</font>

值得注意的是：如果所使用的springboot的版本是大于或等于2.1版本的，那么Mybatis的版本也必须是大于或等于2.1版本的

2.配置文件中进行基础配置，主要是全局配置文件

3.映射配置文件

4.编写sql

5.service层调用dao层

6.controller调用service层



### 新建一个项目并做好配置之后如何检测其与数据库的连接呢？

1.先进行基础配置：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301161427702.png#pic_center)


2.在test类中进行测试，看是否成功打印：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301161439724.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)



### 让Mybatis的dao/mapper层接口起作用有两种方式：

方式一：在dao/mapper层接口上标注@Mapper注解，表示这是一个Mybatis的 mapper 类

方式二：在启动类上添加要扫描的dao/mapper包即可

两种方式实现一种即可！两种都实现也不会冲突！



### properties配置文件中一般要配置关于Mybatis的什么

一般配置mybatis.mapper-location即可！有时候会根据需要配置mybatis.type-aliases-package

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301161505103.png#pic_center)

### 重新理解MVC

M：数据和业务（后端开发人员完成）

C：前后端交接（后端开发人员完成）

V：html页面（前端开发人员完成）



## Spring Security

在web开发中，安全第一位！如果过滤器，拦截器玩的特别溜的话也能实现安全，但是使用框架的话会更方便和简单一些！

做网站的时候：<font color='red'>安全应该在设计之初考虑！</font>因为架构一旦确定，项目也已经完工的情况下，你再去修改就很难了！



市面上比较指明的安全框架：Shrio、SpringSecurity

Shrio、SpringSecurity很像，除了类不一样，名字不一样

它们主要职能：<font color='orange'>认证和授权！</font>

权限：

- 功能权限
- 访问权限
- 菜单权限
- ...拦截器，过滤器



**Spring Security简介**

Spring Security是针对Spring项目的安全框架，也是SpringBoot底层安全模块默认的技术选型，他可以实现强大的Web安全控制，对于安全控制，我们仅需要引入spring-boot-starter-security模块，进行少量的配置，即可实现强大的安全管理！

记住及各类：

- WebSecurityConfigurerAdapter：自定义Security策略
- AuthenticationManagerBuilder：自定义认证策略
- @EnableWebSecurity：开启WebSecurity模式（以后遇到其他的类似@Enablexxx的标注，都是开启某一个功能！）

Spring Security的两个主要目标是“认证”和“授权”（访问控制）。

“认证”（Authentication）

”授权“（Authorization）

这个概念是通用的，而不是只在Spring Security中存在。



官方参看文档：https://docs.spring.io/spring-security/site/docs/5.4.5/reference/html5/



### Spring Security配置

配置类的固定架构：

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        super.configure(http);
    }
}
```

<font color='red'>在包下新建config包，然后在其中新建SecurityConfig配置类</font>：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030116153784.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


<font color='red'>然后去configure()方法中去写相应的代码即可！</font>



写好的授权和认证代码为：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030116155824.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301161616879.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
## Shiro

### 1.什么是Shrio

- Apache Shrio是一个java的安全（权限）框架
- Shrio可以非常容易的开发出足够好的应用，其不仅可以用在JavaSE环境，也可以用在JavaEE环境。
- Shrio可以完成：认证、授权、加密、会话管理、Web集成、缓存等。
- 下载地址：http://shiro.apache.org/



### 2.Shrio的功能介绍

见此博客：https://www.cnblogs.com/woms/p/5993805.html以及https://www.cnblogs.com/kyleinjava/p/10542800.html



关于Shrio的具体学习部分后续再补充！