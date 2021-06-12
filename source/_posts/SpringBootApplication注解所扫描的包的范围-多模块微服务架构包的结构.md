---
title: SpringBootApplication注解所扫描的包的范围-多模块微服务架构包的结构
date: 2021-04-27 21:40:53
categories:
- SpringBoot学习
tags:
- SpringBoot
---

![](http://qiniusave.codeyu.cn/springboot.jpg)

<!--less-->

---

@SpringBootApplication==@Configuration+@EnableAutoConfiguration+@ComponentScan
使用了@SpringBootApplication的注解会扫描<font color=red>**启动类所在的包及其子包！**</font>

#### 所以此处介绍一个多模块微服务架构时包的结构：
1.一般情况下，启动类位于web模块中，那么一般我们<font color=red>**会将web模块中的包目录尽量居于最外层。**</font>
比如：
```
web模块包结构：com.company.robot
然后在该包下可以有其他的子包，比如config包等，但是启动类必须直接放置于com.company.robot包结构下
```
2.对于其他模块包结构是怎样的呢？介绍如下
```
比如service模块：
它可以位于：com.company.robot.service 这样的包结构下

又比如dao模块：
它可以位于：com.company.robot.dao 这样的包结构下

又比如biz模块：
它可以位于：com.company.robot.biz 这样的包结构下
```

3.那么到目前位置会发现：启动类所在的包为com.company.robot，<font color=red>**启动类所在的包及其子包包括了service模块、dao模块、biz模块所在的包，所以启动类可以成功的扫描到这些包中的bean。**</font>

#### 结论：将启动类所在模块的包结构置于其他模块包结构的外层！

如果想自定义扫描包的范围：则可在启动类下加上@ComponentScan注解以覆盖@SpringBootApplication注解默认的扫描范围！