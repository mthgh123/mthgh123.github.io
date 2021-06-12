---
title: String关于null以及集合关于null的判定与认识
date: 2021-05-25 18:23:06
categories:
- 小知识点
tags:
- 小知识点
---

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210324174251932.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<!--less-->

---

### 1.String为null
```java
//定义一个str，此时str为null
String str;

//注意：此时str不为null，此时只是str.length()==0而已
String str = "";
```

### 2.List<String\>为null
```java
//此时的strs不为null，因为strs引用了一个实际存在的集合对象，虽然strs中没有加入元素。即strs==null此时会返回false
//但是此时的strs.size()==0会返回true，即strs不为null，但是其中的元素个数为0
//此时的strs.isEmpty()会返回true
//所以：strs.size()==0  与  strs.isEmpty()==true 是相对应的！
List<String> strs = new ArrayList<>();

 //添加元素之后，此时的strs.size()==1
 //所以：strs.size()>0  与  strs.isEmpty()==false 是相对应的！
 strs.add("123");
```