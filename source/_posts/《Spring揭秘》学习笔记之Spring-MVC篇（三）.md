---
title: 《Spring揭秘》学习笔记之Spring MVC篇（三）
date: 2020-12-07 20:21:34
categories:
- 《Spring揭秘》学习笔记
tags:
- Spring
---

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207203300248.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<!--less-->

## 概述

该部分为学习《Spring揭秘》后第25章的学习笔记，主要是Spring MVC部分的内容。

## 第25章：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207184926909.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207184934533.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207184950258.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


### 25.1 文件上传与MultipartResolver
如果要在基于Spring MVC的Web应用程序中通过表单上传文件，那么MultipartResolver将是在服务器端处理文件上传的主要组件。

#### 25.1.1 使用MultipartResolver进行文件上传的简单分析
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207185248371.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

当web请求类型为multipart的时候，MultipartResolver的resolverMultipart(request)所返回的MultipartHttpServletRequest将被作为后继处理流程所依赖的HtppServletRequest而使用。也就是说，对应最初请求的HtppServletRequest将被“偷梁换柱”为MultipartHttpSer-vletRequest。
下方为MultipartHttpServletRequest接口定义：
Public interface MultipartHttpServletRequest extend 					HttpServletRequest, MultipartRequest {
}

Public interface MultipartRequest {
Iterator getFileNames();
MultipartFile getFile(String name);
Map getFileMap();
}
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207185410421.png#pic_center)

Spring MVC框架内为MultipartResolver提供了两个可用的实现类，即org.springframework.web.multipart.commons.CommonsMultipartResolver。

### 25.2 Handler与HandlerAdapter
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207185440179.png#pic_center)

对于DispatcherServlet来说，它是如何判断我们到底使用的是什么类型的Handler，又如何决定调用Handler对象的哪个方法来处理Web请求？<font color=red>为了能够以统一的方式调用各种类型的Handler，DispatcherServlet将不同Handler的调用职责转交给了一个称为HandlerAdapter的角色：</font>
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207185513390.png#pic_center)

HandlerAdapter得以成为DispatcherServlet和不同Handler的“中间人”，要归功于它的两个主要方法，即supports(..)和handle(..)。至于getLastModified(..)方法，它的主要目的只是为返回给客户端的LastModified这个HTPP头提供相应的时间值。<font color=red>**DispatcherServlet从HandlerMapping获得一个Handler之后，将询问HandlerAdapter的supports(..)方法，以便了解当前的HandlerAdapter是否支持HandlerMapping刚刚返回的Handler类型的调用。**</font><font color=orang>**如果supports(..)返回true，DispatcherServlet则调用HandlerAdapter的handle(..)方法，同时将刚才的Handler作为参数传入。方法执行后将返回ModelAndView，之后的工作就由ViewResolver接手了。**</font>
现在，DispatcherServlet只需要面对HandlerAdapter提供的统一接口，而不需要面对纷繁复杂的Handler类型。支持新的Handler类型，意味着只需要为DispatcherServlet提供新的HandlerAdapter实现即可。DispatcherServlet借助多个HandlerAdapter，调用当前HandlerMapping所返回的Handler来处理Web请求的逻辑。
无论我们想在Spring MVC中使用什么类型的Handler，只要同时为DispatcherServlet提供对应该HandlerAdapter实现就成，DispatcherServlet无需任何变动。

#### 25.2.2 深入了解Handler
到目前为止，我们使用最多的Handler就是Controller。不过，如果Controller不合我们的口味的话，我们可以使用Spring MVC提供的其他类型的Handler，甚至于自定义Handler类型。
1. Spring MVC提供的Handler类型
即org.springframework.web.servlet.mvc.throwawaw.ThrowawayCont-roller。ThrowawayController接口定义了一个方法：
public interface ThrowawayController {
ModelAndView execute() throws Exception;
}
ThrowawayController不同于Controller，它拥有状态（这些状态值将由框架提供数据绑定），所以，添加到WebApplicationContext使用的ThrowawayController需要使用prototype类型的scope，而且，顾名思义ThrowawayController执行后将被抛弃，下次有新的Web请求需要同一类型的ThrowawayController处理，将获得一个新的实例。

#### 25.2.3 近看HandlerAdapter的奥秘
实际上，为具体的Handler类型提供一个HandlerAdapter实现类非常简单。主要工作只是调用这个HandlerAdapter“认识”的Handler的Web请求处理方法，然后将处理结果转换为DispatcherServlet统一使用的ModelAndView。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207185809431.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<font color=red>其次，有了可以返回我们自定义的Handler的HandlerMapping之后，我们要为DispatcherServlet提供能够调用该类型Handler的HandlerAdaptor实现。</font>

### 25.3 框架内处理流程拦截与HandlerInterceptor
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207185850679.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207185909471.png#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207185920198.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<font color=oranage>HandlerInterceptor是一个接口，而不是一个实现类，在你自己的项目中就实现了自己的三个实现类，这三个类均实现了HandlerInterceptor接口。</font>
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207190009419.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

#### 25.3.1 可用的HandlerInterceptor实现
通过查看HandlerInterceptor的继承层次，我们可以发现四五个HandlerInterceptor：UserRoleAuthorizationInterceptor、WebContent-Interceptor、LocaleChangeInterceptor以及ThemeChangeInterceptor等。
<font color=red>你自己的项目中的实现类是实现的HandlerInterceptor接口，然后对于接口中的每一个方法，你只给出了prehandle()方法的具体实现，另外两个方法方法体部分的代码为空的。</font>


#### 25.3.3 HandlerInterceptor寻根
HandlerInterceptor和Handler实际上“本事同根生”。如果我们从HandlerInterceptor所处的位置溯源而上（按照HandlerInterceptor->HandlerExecutionChain->HandlerMapping的顺序），则会发现HandlerMapping实现类的父类，提供了setInterceptors(..)方法以接受一组指定的HandlerInterceptor实例。


### 24、25章内容自我总结：
HandlerMapping用以寻找web请求所对应的handler，HandlerMapping的接口中有一个getHandler()方法，它返回的不是一个handler，而是一个既包含有handler数据又包含有Interceptor数据的HandlerExecutionChain实例。
HandlerMapping接口定义：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207190124556.png#pic_center)



在spring或者spring boot中，HandlerMapping用以寻找web请求所对应的handler，但我们还需要决定当前的web请求应该给Handler中的哪一个方法进行处理，实现这一功能主要是依靠MethodNameResolver接口的实现类来完成的。
MethodNameResolver接口定义：只有一个getHandlerMethodName()方法
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207190223260.png#pic_center)



Controller在处理完之后会返回一个ModelAndView实例，该实例拥有两部分的内容，一部分是<font color=red>视图相关内容</font>，另一部分是<font color=red>模型数据</font>，ModelAndView实例以逻辑视图名的形式或者View实例的形式保存视图相关信息，而将模型数据保存到ModelMap中，在需要使用时，通过View实例进行查找并取用。


ViewResolver接口定义很简单，其只定义了一个方法resolveViewName()，该方法返回一个View实例。
ViewResolver接口的定义：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207190310879.png#pic_center)



View接口也只定义了两个方法，一个是String getContentType()，另一个是Void render(Map model, HttpServletRequest request, HttpServletResponse response)，即通过模型数据model来渲染视图。
View接口的定义：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020120719032660.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)



MultipartResolver:用于处理文件上传的主要组件。当一个web请求到达以后，DispatcherServlet首先会在WebApplicationContext寻找一个MultipartResolver实例，MultipartResolver接口如下图所示：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207190500450.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

通过实例调用isMultipart()方法，判断web请求是否为multipart类型，若是，则会调用resolveMultipart()方法，然后返回MultipartHttpServletRequest，若不为multipart类型，则依然是返回之前传入的HttpServletRequest。<font color=red>**简单点描述就是：如果有文件上传，那么会将原本的HttpServletRequest替换为MultipartHttpServletRequest，否则的话不做处理。**</font><font color=oranage>**DispatcherServlet调用MultipartResolver实例执行上述过程发生在调用HandlerMapping之前。**</font>


HandlerAdapter:之前说到DispatcherServlet会调用HandlerMapping实例并返回一个handler，接下来DispatcherServlet又会让该handler去处理到来的web请求，而实际上handler是如何处理到来的web请求的呢？答案是：通过HandlerAdapter。下图为接口定义：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207190641514.png#pic_center)

首先DispatcherServlet将询问HandlerAdapter实现类的support()方法，看看该HandlerAdapter是否支持HandlerMapping所返回handler类型的调用，若支持，则会紧接着调用HandlerAdapter的handle()方法，该方法传入handler参数，然后对web请求进行处理，最后返回一个ModelAndView实例。


HandlerInterceptor:可以在Handler执行前后对处理流程进行拦截操作。实现相应接口的类只需要重写三个方法即可。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020120719071254.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
参考文献：《Spring揭秘》王福强-著