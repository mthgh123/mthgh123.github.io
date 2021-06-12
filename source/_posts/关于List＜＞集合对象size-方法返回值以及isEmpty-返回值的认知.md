---
title: 关于List＜＞集合对象size()方法返回值以及isEmpty()返回值的认知
date: 2021-06-02 15:27:50
categories:
- Java集合
tags:
- Java集合
---

![](http://qiniusave.codeyu.cn/java%E8%83%8C%E6%99%AF.jpg)

<!--less-->

---

### 概述
之前一直对于`List<>`集合对象中关于`size()`以及`isEmpty()`等的认知不够明确，所以做一下总结如下！

### 看代码：
```java
//测试
public static void main(String[] args) {
    //1.以InitialCapacity=5来初始化列表容器大小
    List<List<String>> stringLists = new ArrayList<>(5);
    System.out.println(stringLists.size());
    System.out.println(stringLists.isEmpty());

    //2.
    for(int i=0; i<5; i++){
        stringLists.add(new ArrayList<>());
    }
    System.out.println(stringLists.size());
    System.out.println(stringLists.isEmpty());

    //3.
    System.out.println(stringLists.get(0).size());
    System.out.println(stringLists.get(0).isEmpty());
}

//返回结果如下：
0
true
5
false
0
true
```
### 总结
一个`List<>`集合对象中如果没有添加元素，则该集合的`size==0`，`isEmpty()`返回`true`（比如上方的案例1）；
一个`List<>`集合对象中只要添加了元素，即使所添加的元素本身`size==0`，`isEmpty()`返回`true`（如案例3），那么外层的集合对象`size`也会增加（比如上方的案例2）；