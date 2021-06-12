---
title: java后端接收List＜Long＞或者List＜String＞类型数据
date: 2021-06-08 20:53:42
categories:
- Java集合
tags:
- Java集合
---

![](http://qiniusave.codeyu.cn/java%E9%9B%86%E5%90%88.jpg)

<!--less-->

---

### 概述

平时在做项目时，当从前端以json字符串格式向后端传递对象时，后端使用`@RequestBody`注解解析为对象，当前端只是传递单个查询参数时，则以`@RequestParam`注解来接收参数！

然后今天突然碰到后端的接收参数为`List<Long>`的情况，突然不知道是使用`@RequestBody`还是`@RequestParam`，查询一些资料后，问题得以解决，遂将解决办法记录如下！

### 可行的解决方案
使用`@RequestBody`！
后端接收参数的代码为：注意使用`@RequestBody`注解时，应该使用`post`方法来传递json数据！
```java
@PostMapping("/delete")
public Result delete(@RequestBody List<Long> ids){
	//todo
}
```
在postman测试工具中，json体为：
```json
[
    1,
    2,
    3
]
```
如果是`List<String>`，与上方代码类似：
```java
@PostMapping("/delete")
public Result delete(@RequestBody List<String> ids){
	//todo
}
```
在postman测试工具中，json体为：
```json
[
    "aaa",
    "bbb",
    "ccc"
]
```