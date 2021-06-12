---
title: git使用小技巧
date: 2021-04-22 16:07:52
categories:
- Git学习
tags:
- Git
---

![](http://qiniusave.codeyu.cn/git.jpg)

<!--less-->

---

### 概述
本片博客主要介绍工作中日常git使用方法！

### 正文
#### 1.checkout 的相关操作
在git中，checkout指的是切换分支或创建分支等操作！

（1）创建分支：
```bash
git branch <分支名>
//比如：git branch dev
```

（2）切换分支：
```bash
git checkout <分支名>
//比如：git checkout dev
```

（3）创建并切换分支：
```bash
git checkout -b <分支名>
//比如：git checkout -b dev
```
#### 2.克隆指定分支操作
命令为：
```bash
git clone -b <分支名称> <仓库地址>
```
#### 3.克隆默认分支操作
命令为：
```bash
git clone <仓库地址>
```
#### 4.使用idea从GitLab拉取指定分支时出现问题解决方案
使用idea直接拉取指定分支的url项目出现错误，如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210427110728300.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
该错误提示说分支仓库不存在，顾无法拉取！

#### 解决方案：
启动git bash软件，新建好相应的文件夹，然后直接运行拉取指定分支的命令：
```bash
git clone -b <分支名> git@git.xxxx/xxx.git
```
实现成功拉取，然后再用idea打开运行即可！

#### 5.上述4中在指定新建的文件夹中拉取项目时，新建的文件夹的名字最好不要含下划线，名字本身也不要过长
因为名字过长，并且名字中含有下划线后，拉下的项目用idea打开后，每一个文件都是红色的，而不是更新为最新状态的无色的。并且修改一个文件后文件也不会变成蓝色的。所以这就说明存在问题。后来update该分支时，提示说无法拉取，<font color=red>原因是本地路径名太长了。</font>所以重新新建一个不含有下划线以及名字更短的文件夹后，在该文件夹重新运行git bash并拉取指定分支项目，这一次则为正常！不会出现上述问题了！