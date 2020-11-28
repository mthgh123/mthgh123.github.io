---
title: python安装第三方库时出现No module named pip问题解决
date: 2020-07-04 12:24:25
categories:
- python第三方库安装
tags:
- python第三方库安装
---

<img src="http://qiniusave.codeyu.cn/python%E5%AE%89%E8%A3%85%E7%AC%AC%E4%B8%89%E6%96%B9%E5%BA%93%E6%97%B6%E5%87%BA%E7%8E%B0No%20module%20named%20pip%E5%B0%81%E9%9D%A2.jpg" style="zoom:50%;" />

<!--less-->

### 注意：需要以管理员身份运行cmd，但不需要将当前目录切换到D:\python\scripts目录下，直接在C盘下就可以，因为python当初配置过相应的环境变量

依次执行下列两条指令：

1.会安装相应版本的pip

> python  -m  ensurepip

之后：

2.将上方安装的pip更新为最新版本

> python  -m  pip  install  --upgrade  pip

![](http://qiniusave.codeyu.cn/python%E5%AE%89%E8%A3%85%E7%AC%AC%E4%B8%89%E6%96%B9%E5%BA%93%E6%97%B6%E5%87%BA%E7%8E%B0No%20module%20named%20pip%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B301.PNG)