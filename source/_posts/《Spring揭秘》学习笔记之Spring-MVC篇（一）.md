---
title: 《Spring揭秘》学习笔记之Spring MVC篇（一）
date: 2020-12-07 20:16:04
categories:
- 《Spring揭秘》学习笔记
tags:
- Spring
---

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207163609803.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<!--less-->

## 概述

该部分为学习《Spring揭秘》后第22和23章的学习笔记，主要是Spring MVC部分的内容。

## 第22章：
MVC：
1.控制器负责接收视图发送的请求并进行处理。
2.模型通常封装了应用的逻辑以及数据状态，当控制器通知模型进行状态更新的时候，模型封装的相应逻辑将被调用。（个人觉得：模型实现的功能相当于service层以及dao层的作用）
3.视图是面向用户的接口

当前的Web开发框架有如下两种模型：
**1.请求驱动Web框架**。又称为request/reponse框架。这种框架是基于servlet的请求相应（request/reponse）处理模型构建的。Structs、spring MVC框架都是这种请求驱动的Web开发框架。
**2.事件驱动Web开发框架**。这种框架采用Swing等GUI开发框架类似的思想。

对于请求驱动的Web框架，它们大多是在JSP Model 2的基础上发展而来的。spring MVC框架就是。这些框架通常会结合Front Controller以及Page Controller模式，即原来的单一的Servlet作为整个应用程序的Front Controller。该Servlet收到具体的Web处理请求后，会参照预先可配置的映射信息，将待处理的Web处理请求转发给次一级的控制器（sub-controller）来处理。如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207153541439.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)



## 第23章：
### 1.
Spring MVC对请求处理期间涉及的各种关注点进行了合理而完全的分离：
1.设置HandlerMapping用于处理Web请求与具体请求处理控制器之间的映射匹配。
2.设置LocaleResolver用于国际化处理
3.设置ViewResvoler用于灵活的摄图选择等。
Spring运用逻辑命名视图策略，通过引入ViewResolver和View，清晰地分离了视图类型的选择和渲染（Render）与具体控制器之间的耦合，使得各种视图技术可以很容易地集成到Spring MVC框架中。

### 2.
Spring MVC框架的处理控制器的实现策略，与其他的请求驱动的Web框架在总体思路上是相似的，就如我们之前所说的那样，通过引入Front Controller和Page Controller的概念来分离流程控制逻辑与具体的Web请求处理逻辑。org.springframework.web.servlet.DispatcherServlet就是Spring MVC框架中的Front Controller，它负责接受并处理所有的Web请求，只不过针对具体的处理逻辑，它会委派给它的下一级控制器去实现，即org.springframework.web.servlet.mvc.Controller，而org.springframework.web.servlet.mvc.Controller则对应Page Controller的角色定义，具体关系如下图所示：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207153347836.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

### 3.DispatcherServlet的处理流程可以简单概括如下：
**1.HandlerMapping先生（Web请求的处理协调人）**
DispatcherServlet是整个框架的Front Controller，当将它注册到web.xml时，就注定了它要服务于规定的一组Web请求的命运，而不是单一的Web请求。

Spring MVC为了能够灵活地处理映射的匹配，引入了org.springframework.web.servlet.HandlerMapping来专门管理Web请求到具体的处理类之间映射关系。在Web请求到达DispatcherServlet之后，Dispatcher将寻求具体的HandlerMapping实例，以获取对应当前Web请求的具体处理类，即org.springframework.web.servlet.Controller。

**2.org.springframework.web.servlet.Controller（web请求的具体处理）**
org.springframework.web.servlet.Controller是对应DispatcherServlet的次级控制器，它本身实现了对应某个具体Web请求的处理逻辑。在我们所使用的HandlerMapping查找到当前Web请求对应哪个org.springframework.web.servlet.Controller的具体实例后，DispatcherServlet即可获得HandlerMapping所返回结果，并调用org.springframework.web.servlet.Controller的处理方法来处理当前的Web请求。
org.springframework.web.servlet.Controller的处理方法执行完毕之后，将返回一个org.springframework.web.servlet.ModelAndView实例，ModelAndView包含了如下两部分信息。
1.视图的逻辑名称 2.模型数据
有了ModelAndView所包含的视图与模型二者的信息之后，DispatcherServlet就可以着手视图的渲染工作了。

**3.ViewResolver和View**
Spring提出了一套基于ViewResolver和View接口的Web视图处理抽象层，以屏蔽Web框架在使用不同的Web视图技术时候的差异性。即spring MVC通过引入org.springframework.web.servlet.View接口定义，来统一地抽象视图的生成策略。之后，DispatcherServlet只需要根据Spring Controller处理完毕后通过ModelAndView返回逻辑视图名称查找到具体的View实现，然后委派该具体的View实现类来根据模型数据，输出具体的视图内容即可。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207153646637.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207153717227.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

 

## 22、23章内容自我总结：
DispatcherServlet是Spring MVC的核心类，<font color=oranage>它负责接受并处理所有的Web请求</font>，只不过针对具体的处理逻辑，它会委派给它的下一级控制器（Controller）去实现。

DispatcherServlet相当于是Front Controller，而下一级控制器（Controller）则就相当于Page Controller

下一级控制器（Controller）是<font color=oranage>针对当前Web请求的具体处理类</font>，它本身实现了对应某个具体Web请求的处理逻辑。

Spring MVC是如何将一个Web请求与具体的Controller进行匹配的，其主要是通过HandlerMapping类来实现的，<font color=oranage>这个类的作用是专门管理Web请求到具体的处理类之间的映射关系的。</font>具体过程为：web请求到达DispatcherServlet之后，Dispatcher将寻求具体的HandlerMapping实例，以获取对应当前Web请求的具体处理类（Controller）。

**<font color=red>***需要理清的调用逻辑：一个Web请求到达服务器后，是DispatcherServlet通过HandlerMapping来知道该Web请求对应到哪一个次级控制器（Controller），然后DispatcherServlet再去调用该控制器（Controller）去处理该Web请求。</font>**

<font color=oranage>***Controller方法处理相应的web请求后，会返回一个ModelAndView实例，然后，DispatcherServlet会通过该ModelAndView实例的逻辑视图名称查找到具体的View实现，然后委派该具体的View实现类来根据模型数据输出具体的视图内容。**</font>
*上面说DispatcherServlet会通过该ModelAndView实例的逻辑视图名称查找到具体的View实现，起到将ModelAndView实例的逻辑视图名与具体的View实现类对应起来作用的是org.springframework.web.servlet.ViewResolver类，它会根据ModelAndView的逻辑视图名查找到对应的View实现类，然后将结果返回给DispatcherServlet。

<font color=red>*****通过下图可以看到，一系列的操作都是由DispatcherServlet类来主导的，其作用包括：
1.当一个web请求到来后，DispatcherServlet委派HandlerMapping来获取处理web请求的控制器（Controller）。
2.调用控制器（Controller）来处理web请求。
3.委派ViewResolver来解析控制器（Controller）返回的ModelAndView实例的逻辑视图名，以获取对应的View实现类。（此处的View实现类指的就是static文件中的哪一个.html文件）
4.将ModelAndView中的模型数据交给View来进行最终的视图渲染工作。**</font>

根据之前学习的《jsp&servlet学习笔记》中介绍的知识可知，当一个web请求/HTPP请求到来后，服务器会将该请求封装为一个Java对象，即HttpServletRequest对象，然后通过相应方法可获取到该请求体的具体请求信息。上面第1条说<font color=gree>DispatcherServlet会委派HandlerMapping来获取处理web请求的控制器（Controller）</font>，那么HandlerMappring应该是从HttpServletRequest对象来获取一系列的请求信息的，然后根据请求信息解析出对应的Controller。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207153717227.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
参考文献：《Spring揭秘》王福强-著