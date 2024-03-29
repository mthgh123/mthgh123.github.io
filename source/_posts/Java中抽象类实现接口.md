---
title: Java中抽象类实现接口
date: 2021-05-06 20:15:53
categories:
- java
tags:
- java
---

![](http://qiniusave.codeyu.cn/java%E8%83%8C%E6%99%AF1.jpg)

<!--less-->

---

### 抽象类
Java中的抽象类主要是作为一个公共父类的作用，它可以抽象出用来描述一种类型应该具备的基本特征与功能，具体行为则由子类通过方法重写来完成。

##### 抽象类具有的性质
 - 类定义前需要加上`abstract`关键字
 - 抽象类中可以没有抽象方法
 - 抽象类中可以有非抽象方法
 - 如果一个类中有至少一个抽象方法，那么它一定是抽象类，则类名前必须加上`abstract`关键字，抽象方法没有方法体。
 - 抽象类无法实例化
 - 只有覆盖了抽象类中所有的抽象方法后，其子类才可以实例化。如果存留未实现的抽象方法则该子类仍为一个抽象类，无法创建对象。

##### 抽象类的意义
抽象类往往用来表示对问题领域进行分析、设计中得出的抽象概念。其存在的意义在于其设计性、复用性与扩展性。抽象类方便了具体类的定义。

### 抽象类可以实现接口
抽象类实现接口其所具有的意义：
1.一般而言，多使用普通类来实现接口，但是普通类实现接口的话就必须实现接口的所有方法，这样容易造成代码冗余。
2.而如果使用抽象类来实现接口，然后再用普通类继承抽象类的话，其仍然可以实现接口中但抽象类中并未实现的方法。以此可以只实现必要的方法，即抽象类中可以不定义对于子类而言必要的方法，而最终交由子类自己来实现。
3.从2中可知，抽象类实现接口时，不必实现接口中的所有方法，未实现的方法可以交由子类来实现。