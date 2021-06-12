---
title: static关键字的一点点小结
date: 2021-04-30 18:12:52
categories:
- java
tags:
- java
---

![](http://qiniusave.codeyu.cn/java%E8%83%8C%E6%99%AF.jpg)

<!--less-->

---

### 概述
本篇博客介绍Java类中用static关键字修饰的方法以及实例变量所具有的特点！

### 特点1：static修饰字段与非static字段区别
1.用static修饰的实例变量或者类，它们是类共同拥有的，即属于某一个类的，而不属于对象，即全局唯一，假设实例化了好几个该类的对象，那么这些对象中static修饰的字段的值是共享的，即全局唯一；2.static修饰的字段存储在固定的内存空间处；3.在分配资源方面，首先给static关键字修饰的分配存储空间，之后再给非static分配存储空间！<font color=red>**也就是先初始化static字段和static方法，然后才初始化非static字段和方法！**</font> 

### 特点二2：独特的引用方式
用static修饰的字段和方法可以用：<font color=red>**类名.静态方法名**</font>   或者   <font color=red>**类名.静态字段名**</font>  的方式进行引用。

### 特点三：一般而言，static方法中只能引用static方法，若要引用非static方法，则需要实例化一个类对象，再通过类对象在static方法中引用非static方法
见下述例子：

```java
public class Test{
	//main方法是static方法
	public static void main(String[] args) throws Exception {
		new Test().printHello();
	}

	private void printHello(){
		System.out.println("Hello");
	}
}
```
通过上面的例子可以看出，由于main方法是static方法，所以它在调用非static方法`printHello()`时首先`new`了一个`Test对象`，然后再通过实例对象去调用的`printHello()`方法！
如果main方法中类似于下方所示的引用方式，则会报错：
```java
public static void main(String[] args) throws Exception {
	//直接调用，而不是通过对象调用
	printHello();
}
```

### 为什么会出现上述状况呢？
<font color=red>**那是因为JVM会先为静态变量或者静态方法分配内存，也就是静态变量或者静态方法先初始化，而这个时候普通成员变量或方法还未初始化！**</font>