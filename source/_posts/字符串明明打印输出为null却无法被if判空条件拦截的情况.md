---
title: 字符串明明打印输出为null却无法被if判空条件拦截的情况
date: 2021-03-24 16:35:58
categories:
- 小知识点
tags:
- 小知识点
---

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210324174251932.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<!--less-->

---


今天在解析Json数据时碰到一个超级奇怪的现象：

在浏览器上查询得到的Json数据为（以xml形式显示）：

```xml
<intentionResponse>null</intentionResponse>
```

然后我自己的java代码为：

```java
public static String extractIntentionResponse(String intentionResponse){
    if(intentionResponse!=null) {
        System.out.println(intentionResponse);
    }
//sout打印输出结果为：null
```

我当时就震惊了，你不是null吗！那你咋还能进入`if`判断条件内部啊。

此时就显现出debug的强大威力了：

进入debug，查看具体对象，发现：<font color=red>intentionResponse="null"</font>

所以找到原因了，intentionResponse不是null对象，而是"null"字符串！