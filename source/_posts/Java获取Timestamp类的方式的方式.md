---
title: Java获取Timestamp类的方式的方式
date: 2021-05-13 17:46:42
categories:
- java
tags:
- java
---

![](http://qiniusave.codeyu.cn/java%E6%96%87%E7%AB%A0%E5%B0%81%E9%9D%A2.jpg)

<!--less-->

---

Java中获取Timestamp类的方式的方式
```java
//系统类的函数获取
Timestamp time1 = new Timestamp(System.currentTimeMillis());  

//通过Date类对象获取  
Timestamp time2 = new Timestamp(new Date().getTime());  
  
//通过Calendar类获取
Timestamp time3 = new Timestamp(Calendar.getInstance().getTimeInMillis()); 

//通过joda time中的DateTime类获取
Tiemstamp time4 = new Timestamp(DateTime.now().getMillis());
```