---
title: java中null字符串与字符串长度为零的区别
date: 2021-03-23 10:52:26
categories:
- 小知识点
tags:
- 小知识点
---

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210324174251932.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<!--less-->

---

在java中，null字符串指的是字符串对象连内存都不占用，就如同：
```java
String str;
```
`str`不指向任何对象，即不指向任何内存地址，此即为null

而长度为0的字符串是存在内存空间的，如下：
```java
String s1 = new String("");
//只不过此时 s1.length()==0
```
此时的s1指向字符串`""`所在的内存地址空间！