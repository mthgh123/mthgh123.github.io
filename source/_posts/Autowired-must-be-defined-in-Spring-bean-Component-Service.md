---
title: Autowired must be defined in Spring bean @Component/@Service
date: 2021-05-28 15:44:56
categories:
- Exception处理
tags:
- Exception
---

![](http://qiniusave.codeyu.cn/exception%E8%83%8C%E6%99%AF.jpg)

<!--less-->

---

### 概述
今天写代码时碰到一个问题，现记录如下：今天在一个类中注入一个bean对象，结果提示Autowired members must be defined in valid Spring bean (@Component|@Service|...)，它的含义就是：<font color=red>**自动注入对象必须定义在有效的spring bean内**</font>，也就是说只有本身作为bean的类才能注入其他对象。

报错代码如下：
```java
public class LogQueryBiz {
    @Autowired
    private ErrorDetailService errorDetailService;
	
	//todo
}
```
此时`@Autowired`上会有黄色警告，并提示：Autowired members must be defined in valid Spring bean (@Component|@Service|...)

修改代码为如下所示时成功解决问题：
```java
@Component
public class LogQueryBiz {
    @Autowired
    private ErrorDetailService errorDetailService;
	
	//todo
```

这也正是警告信息所描述的，<font color=red>**自动注入对象必须定义在有效的spring bean内**</font>，即需要将`LogQueryBiz`定义为`bean`，才能在该类中注入其他`bean`。

<font color=red>**扩展知识：一般而言，标注有`(@Component|@Service|...)`注解的类中不应该有static方法和static变量，这是因为标注有`(@Component|@Service|...)`的类本身是以对象来调用方法的，即方法是属于对象的，而static标注的方法和成员数据都是属于类的。**</font>