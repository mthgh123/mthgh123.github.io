---
title: 新建spring boot项目后maven插件画红色波浪线问题
date: 2020-12-22 11:12:00
categories:
- SpringBoot学习
tags:
- SpringBoot
---

![](http://qiniusave.codeyu.cn/springboot.jpg)

<!--less-->

---

### 问题描述：
在新建spring boot项目之后，maven插件本身没有成功导入，比如：maven-jar-plugin下画红色波浪线。

### 解决方案：
**1.到自己的Maven仓库下去看实际的jar包是否存在。**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208170229145.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


**2.先来到该路径下**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208170242897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


**3.到相应的文件夹中去查看jar包，比如：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208170254106.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

**画红色下划线的jar包位于org->apache->maven->plugins文件夹下：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208170306216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


**4.进入该文件夹，发现了一系列版本的maven-jar-plugin文件夹：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208170320129.png#pic_center)

**我自己的项目需要的是3.2.0版本的jar包，如下图所示：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208170329245.png#pic_center)


**在之前这个地方是画有红色波浪下划线的，主要原因是我到3.2.0文件夹中去看到并没有maven-jar-plugin-3.2.0.jar文件，那如何把maven-jar-plugin-3.2.0.jar文件成功导入名称为3.2.0的文件夹内呢？**

**<font color=red>解决方案：将下图所示的3.2.0文件夹直接删除：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208170320129.png#pic_center)
**<font color=red>然后点击idea软件的reimport即可（点击下图按钮）：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201208170441422.png#pic_center)

**之后，在org->apache->maven->plugins文件夹下会新生成一个3.2.0文件夹，其中含有相应的jar包，之后，idea中出现的红色下画波浪线也会消失。**

**问题成功解决！**