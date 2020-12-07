---
title: 《Spring揭秘》学习笔记之Spring MVC篇（二）
date: 2020-12-07 20:19:07
categories:
- 《Spring揭秘》学习笔记
tags:
- Spring
---

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207203212225.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<!--less-->

## 概述

该部分为学习《Spring揭秘》后第24章的学习笔记，主要是Spring MVC部分的内容。

## 第24章：
### 24.1 忙碌的协调人HandlerMapping
HandlerMapping帮助DispatcherServlet进行Web请求的URL到具体处理类的匹配。又或者说<font color=oranage>HandlerMapping要处理的是Web请求到相应Handler之间的映射关系。</font>
下方为HandlerMapping接口的定义：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207165335998.png#pic_center)
23章中曾说HandlerMapping是返回处理相应web请求的Controller，但此处的getHandler()方法为何返回的是HandlerExecutionChain类呢？
在《spring技术内幕》这本书中有说：即HandlerExecutionChain它本身持有一个Interceptor链和一个Object类型的handler对象，这个handler对象实际上就是与web请求对应的控制器（Controller or …），而所持有的Interceptor链可以为handler对象提供功能的增强。
下方给出HandlerExecutionChain部分实现代码：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020120716563581.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
#### 24.1.1 可用的HandlerMapping
1.BeanNameUrlHandlerMapping
2.SimpleUrlHandlerMapping (了解一下这个即可)
3.ControllerClassNameHandlerMapping
4.DefaultAnnotationHandlerMapping

#### 24.1.2 HandlerMapping执行序列（Chain Of HandlerMapping）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207170221540.png#pic_center)


### 24.2 我们的亲密伙伴Controller
Controller是Spring MVC框架支持的用于处理具体Web请求的handler<font color=red>类型之一</font>。
Spring MVC提供了一套Controller实现体系，它帮助我们更好的处理了Web请求处理过程中的某些通用关注点。
下图给出了Spring MVC中Controller的继承层次体系：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020120717025547.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
**1.自由挥洒派Controller**。之所以将AbstractController和MultiActionController归为自由挥洒一排，是因为基本上只要你熟悉Servlet SPI，就可以随意处理Web请求。如果你是从Servlet时代走过来的，那么会发现这一派的做事风格让你觉得很亲切，从HttpServletRequest中获取参数，然后验证，调用业务层逻辑，最终返回一个ModelAndView，甚至，你可以直接通过HttpServletRequest输出最终的视图。不过自由倒是自由了，杂七杂八要处理的细节也很多，所以，使用AbstractController和MultiActionController之前最好考察一下当前场景是否合适。如果要处理的底层细节过多，那么不妨求助一下“规范操作”的各位仁兄！
**2．规范操作派的Controller**。以BaseCommandController为首的规范操作一派，对Web处理过程中的某些通用逻辑做了进一步的规范化封装处理，规范化的主要方面包括：
（1）主动抽取请求参数并绑定到指定的Command对象；
（2）提供了统一的数据验证方式，BaseCommandController及其子类可以接收一组org.springframework.validation.validator以进行数据验证，我们可以根据具体数据提供相应的Validator实现；
（3）规范化了表单（Form）请求的处理流程，并且对简单的多页面表单请求处理提供支持。
虽然这套“制度”对我们的自由发挥进行了限制，但只要我们熟悉这套规则，依然可以游刃有余其间。

#### 24.2.1 AbstractController
AbstractController是整个Controller继承层次的起源，该类通过模板方法帮我们解决了如下几个通用关注点：
（1）管理当前的Controller所支持的请求方法类型（GET/POST）
（2）管理页面的缓存设置，及是否允许浏览器缓存当前页面
（3）管理执行流程在会话（Session）上的同步
而我们所要做的，只不过是在AbstractController所公开的handleRequestInternal(request,reponse)模板方法中实现具体Web请求处理过程的其他逻辑。
虽然直接继承AbstractController实现Web处理逻辑没有太多限制，但往往需要关注太多的细节，比如参数抽取，数据验证之类。所以，对于简单的Web请求，可以通过这种方式来处理，如果涉及表单相关的Web请求，最好使用BaseCommandController一族的相应子类进行扩展，如SimpleFormController。

#### 24.2.2 MultiActionController
对于一组逻辑上相近的Web请求或者<font color=red>针对统一对象甚至多个对象的一组查询操作</font>，我们都可以将这些Web请求交给MultiActionController来统一处理而不用为每一个Web请求单独实现一个继承AbstractController的处理类。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207170914731.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<font color=orange>上方的MultiActionController类与现在的spring Boot中使用的Controller类十分相似，即一个类中有一系列的处理方法。那么，对于spring而言，如何让MultiActionController知道，当一个Web请求进来之后，将由哪个方法来处理它？答案是MethodNameResolver接口类</font>

<font color=red>*****org.springframework.Web.servlet.mvc.multiaction.MethodNameResolver的主要工作，就是帮助MultiActionController决定当前Web请求应该交给哪个方法来处理。**</font><font color=oranage>在spring boot中应该也有类似的类，帮助决定由controller类中的哪一个方法来处理当前的web请求。说不定就是下方说所的三种具体实现：</font>
MethodNameResolver本身作为一个策略接口定义，在Spring MVC框架内默认提供了如下三种策略实现。
（1）InternalPathMethodNameResolver
（2）PropertiesMethodNameResolver
（3）ParameterMethodNameResolver

#### 24.2.3 SimpleFormController
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207182452249.png#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207182505943.png#pic_center)

### 24.3 ModelAndView
通常，Controller在将Web请求处理完成后，会返回一个ModelAndView实例。<font color=oranage>**该ModelAndView实例将包含两部分内容，一部分为<font color=orange>视图相关内容</font>，可以是<font color=red>逻辑视图名称</font>，也可以是具体的<font color=red>View实例</font>；另一部分则是<font color=orange>模型数据</font>，视图渲染过程中将会把这些模型数据合并入最终的视图输出。**</font>
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020120718292873.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


#### 24.3.1 ModelAndView中的视图信息
恰如两组构造方法所表明的那样，ModelAndView可以以<font color=red>逻辑视图名的形式</font>或者<font color=red>View实例的形式</font>来保存视图信息。
（1）View实例的形式：如果ModelAndView中直接返回了View实例，那么，DispatcherServlet将直接从ModelAndView中获取该View实例并渲染视图。
（2）逻辑视图名的形式：DispatcherServlet将寻求ViewResolver的帮助，根据ModelAndView中的逻辑视图名称获取一个可用的View实例，然后再渲染视图。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207183217816.png#pic_center)


#### 24.3.2 ModelAndView中的模型数据
ModelAndView<font color=red>以org.springframework.ui.ModelMap的形式来保持模型数据</font>，通过构造方法传入的或者通过实例方法添加的模型数据都将添加到这个ModelMap中。至于ModelMap中保持的模型数据将会在视图渲染阶段，由具体的View实现类来获取并使用。

### 24.4 视图定位器ViewResolver
<font color=gree>ViewResolver的主要职责是：根据Controller所返回的ModelAndView中的<font color=red>逻辑视图名</font>，为DispatcherServlet返回一个可用的<font color=red>View实例</font>。</font>

实际上的ViewResolver接口定义很简单，入下图：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207183404801.png#pic_center)

大部分的ViewResolver实现类，除了org.springframework.web.serv-let.view.BeanNameViewResolver是直接实现ViewResolver接口的，其余的都是直接或间接的继承自org.springframework.web.servlet.view.Ab-stractCachingViewResolver。因为针对每次请求都重新实例化View将可能为Web应用程序带来性能上的损失，所以Spring MVC在<font color=red>AbstractCachingViewResolver这一继承层次加入了View实例的缓存功能。</font>

#### 24.4.1 可用的ViewResolver实现类
将Spring MVC提供的ViewResolver划分为两类，一类称为<font color=red>面向单一视图类型的</font>ViewResolver，另一类则称为<font color=red>面向多视图类型的</font>ViewResolver。
1.面向单一视图类型的ViewResolver
该类别ViewResolver的正宗名称应该是UrlBasedViewResolver（它们都直接地或者间接地继承自该类）。使用该类别地ViewResolver，我们不需要为它们配置具体的逻辑视图名到具体View的映射关系。通常只要指定一下视图模板所在的位置，这些ViewResolver就会按照逻辑视图名，抓取相应的模板文件，构造对应的View实例并返回。
属于该类别的主要ViewResolver实现类为如下几个：
（1）InternalResolverViewResolver
（2）FreeMarkerViewResolver/VelocityViewResolver
2. 面向多视图类型的ViewResolver
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207183846518.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

面向多视图类型的ViewResolver主要有三个：
（1）ResourceBundleViewResolver
（2）XmlViewResolver
（3）BeanNameViewResolver

#### 24.4.2 ViewResolver查找顺序（Chain Of ViewResolver）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207183917981.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


### 24.5 各司其职的View
org.springframework.web.servlet.View是Spring MVC中将原本可能存在于DispatcherServlet中的视图逻辑得以剥离出来的关键组件。通过引入该策略抽象接口，我们可以极具灵活性地支持各种视图渲染技术。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207183946505.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


#### 24.5.1 View实现原理回顾
一个View实现类所要做的，就是使用相应的技术API将模板和最终提供的模板数据合并到一起，最终输出结果页面给客户端。
#### 24.5.2 可用的View实现类
Spring MVC提供的View实现类都直接或间接继承自org.springframework.web.servlet.view.AbstractView。该类定义了大多数View实现类都需要的一些属性和简单的模板化的实现流程。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207184015106.png#pic_center)
参考文献：《Spring揭秘》王福强-著