---
title: 读取File或MultipartFile文本文件中的每一行数据
date: 2021-04-05 13:00:02
categories:
- 小知识点
tags:
- 小知识点
---

![](http://qiniusave.codeyu.cn/springmvc.jpg)

<!--less-->

---

### 概述
对于以文本文件读取的MultipartFile或File类型的文件，如何读取文本文件中的每一行并进行操作呢？

### 1.对于MultipartFile文件
```java
/*
GBK编码方式：
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(multipartFile.getInputStream(),"GBK"));
UTF8编码方式：
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(multipartFile.getInputStream(),"UTF8"));
*/
//下方multipartFile为MultipartFile文件
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(multipartFile.getInputStream()));
String lineTxt;
while ((lineTxt=bufferedReader.readLine())!=null){
	System.out.println(lineTxt);
}
```


### 2.对于File文件
```java
/*
GBK编码方式：
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(new FileInputStream(file),"GBK"));
UTF8编码方式：
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(new FileInputStream(file),"UTF8"));
*/
//下方file为File类型文件
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(new FileInputStream(file)));
String lineTxt;
while ((lineTxt=bufferedReader.readLine())!=null){
	System.out.println(lineTxt);
}
```