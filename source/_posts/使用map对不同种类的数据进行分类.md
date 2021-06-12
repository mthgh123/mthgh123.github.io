---
title: 使用map对不同种类的数据进行分类
date: 2021-04-14 17:33:07
categories:
- 小知识点
tags:
- 小知识点
---

![](http://qiniusave.codeyu.cn/hashmap.jpg)

<!--less-->

---

### 概述

对于一批数据，其输入方式为`List<Entity>`，假设现需要根据不同的`symble`值（假设不同symble值的种类未知），将`List<Entity>`进行分类，然后以`List<List<Entity>>`的形式进行返回。

```java
public class Entity{
    private String symble;
    private T data;
}
```

### 思路如下：

```java
public List<List<Entity>> group(List<Entity> entities){
    Map<String,Integer> map = new HashMap<>();
    int symbleNum = 0; //symbleNum记录List<Entity>中symble的种类
    
    //下方for循环执行完毕后map中存储<Entity,Integer>，Integer对应到之后List<List<Entity>>的下标索引，通过下标索引取出Entity实现分类
    for(int i=0; i<entities.size(); i++){
        String symble = entities.get(i).getSymble();
		if(!map.containsKey(symble)){
            map.put(symble,symbleNum++);
        }
    }
    
    List<List<Entity>> listListEntity = new ArrayList<>();
    //下方的for循环很关键，因为上方的List<List<Entity>> listListSub = new ArrayList<>();只是新建了最外侧的集合，而每一个集合元素为null，下方的for循环就是填充每一个集合元素，新建每一个集合元素
    for(int i=0; i<symbleNum; i++) {
        listListEntity.add(new ArrayList<>());
    }
    
    //下方for循环实现分类
    for(int i=0; i<symbleNum; i++){
        for (int j = 0; j < entities.size(); j++) {
            String symble = entities.get(i).getSymble();
            
            //通过map获取到对应于List<List<Entity>>中的位置索引，从而实现分类
            Integer index = map.get(symble);
            //将Entity类填入其所属位置
            listListEntity.get(index).add(entities.get(i));
        }
    }
    return listListSub;
}
```

### 值得学习的思路以及注意点：

1.通过map实现分类，其可实现将种类不确定，个数不确定的数据进行分类

2.新建`List<List<Entity>> listListEntity = new ArrayList<>();`后，并不能通过`listListEntity.get(i)`获取对应下标的元素，因为`listListEntity.get(i)`仍为`List<Entity>`，而它们并未被新建出来，所以需要先新建出来才可获取到！即通过for循环为每一个位置添加一个`List<Entity>`，如：

```java
for(int i=0; i<symbleNum; i++) {
    listListEntity.add(new ArrayList<>());
}
```