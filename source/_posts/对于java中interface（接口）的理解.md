---
title: 对于java中interface（接口）的理解
date: 2020-05-02 12:09:36
categories:
- java
tags:
- java
---

<img src="http://qiniusave.codeyu.cn/java%E6%96%87%E7%AB%A0%E5%B0%81%E9%9D%A2.jpg" style="zoom:30%;" />

<!--less-->

---

### java中的interface

​	初学java时，对java中的接口的具体作用总是不太理解。

​	接下来结合个人认识以及书上关于接口的说明谈一谈对接口的理解。

### 首先来看一看书上对于接口的描述：

​	1.接口主要用来描述类具有什么功能，而并不给出每个功能的而具体实现。

​	2.一个类可以实现一个或多个接口。

​	3.接口中的所有方法自动的属于public。因此，在接口中声明方法时，不必提供关键字public。如：

```java
public interface Comparable
{
    int compareTo(Object other);   //该方法的前面未加public
}
```

​	4.在接口实现时必须把方法声明为public。否则，编译器将认为这个方法的访问属性是包可见性，之后编译器会试图提供更严格的访问权限的警告信

```java
public int compareTo(Object otherObjiect)
{
    //具体实现代码
}
```

​	5.接口不能实例化，即不能用new运算符实例化一个接口。但接口变量可以引用实现了接口的类的对象。也可以把实现了接口的类的对象强制转换为接口的对象。

如：

```java
class Employee implements Comparable
{
    //具体实现代码
}
//则接口变量可以引用实现了接口的类的对象
Comparable A=new Employee();
//把实现了接口的类的对象强制转换为接口的对象。
Employee B=new Employee();
Comparable A=(Employee) B;
```

​	6.可以把接口看做是没有实例域的抽象类。更进一步的讲：接口带来了类似于c++的多重继承的优点，而避免了多重继承所带来的复杂性和低效性的缺点。

### 如何理解接口：

​	就如同上面第一条所说的：<font color=red>接口主要用来描述类具有什么功能。</font>

​	也就是说看到一个类实现了什么样的接口，那么我们就能大概知道这个类具有什么样的功能。

​	比如：

```java
class Employee implements Clonable,Comparable
```

Employee类实现了Clonable,Comparable这两个接口，那么就知道了这个类可比较和可克隆。



java中只能继承一个父类，很多时候父类所具有的方法不能满足子类的需求，比如Employee类继承自抽象的People类，People类可能具有吃、住、行等方法。而Employee类可能还需要定义关于如何工作的work()方法。

有人可能要问，为什么不在Employee类中把克隆、比较直接以类似于实现work()方法一样来定义呢？

比如，直接在Employee类中定义clone()方法和compare()方法。

对此，我的理解是（同时也参考了另外一篇文章的作者的理解）：<font color=red>接口主要用来描述类具有什么功能。对于那些普遍存在于类中的方法，如对象间的可比较方法、对象的直接克隆方法，当我们看到这个类定义了这些接口的时候，也就知道了这个类具有了这些功能。这也正对应了书上说的那句话：接口主要用来描述类具有什么功能</font>

```java
abstract class People
{
    //具体实现
    public void eat(){xxx;}
    public void live(){xxx;}
    public void run(){xxx;}
}

class Employee extends People  implements Clonable,Comparable
{
    //具体实现
    @override
    public void eat(){xxx;}
    
    @override
    public void live(){xxx;}
    
    @override
    public void run(){xxx;}
    
    public void work(){xxx;}
    
    //Clonable接口的方法实现
    @override
    public int compareTo(Object other){xxx;}
    
    //Clonable接口的方法实现
    @override
    public Employee clone(){xxx;}
    
}
```



参考：https://blog.csdn.net/whandwho/article/details/80247521