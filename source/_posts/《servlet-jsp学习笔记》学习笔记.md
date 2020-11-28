---
title: 《servlet&jsp学习笔记》学习笔记
date: 2020-11-28 10:51:25
categories:
- servlet
tags:
- servlet

---

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128105459534.gif#pic_center)

<!--less-->

## 概述

<font color=red>本书仅前5章为重点，最重要的也就是3、4、5，但前两章介绍了servlet的作用，也很重要
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128102545893.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
需要重点认识的几个类：HttpServletRequest、HttpServletResponse、HttpSession以及ServletContext
重点搞清楚原理知识，比如一个HTTP请求发送至Tomcat服务器，接下来后续的一系列处理步骤是怎么样的？

## 第一章：
了解HTML、HTTP、URI、URL的基本概念：
通俗理解：URL是RUI的子集。URL：指以文字方式说明互联网上的资源如何获得。

浏览器与web网站之间的沟通方式是http协议，它有两个基本单极为重要的特性：
（1）	基于请求（Request）/响应（Response）模型
（2）	无状态（Stateless）协议：web网站响应客户端之后，就不会记得客户端的信息

Web容器（Container）是Servlet/JSP唯一认得的HTTP服务器。如果希望用Servlet/JSP编写Web应用程序可以正常运行，就必须知道Servlet/JSP如何与Web容器沟通，We容器如何管理Servlet/JSP的各种对象等问题。

对于Java而言，JVM是其操作系统，.java文件会编译为.class文件，.class对于JVM而言，就是其可执行文件。Java程序基本上只认得一种操作系统，那就是JVM。

对于编写Servlet/JSP来说，容器的概念更广，它不仅持有对象，还负责对象的生命周期与相关服务的连接。

编写Servlet时，会接触HttpServletRequest、HttpServletReponse等对象，想想看，HTTP那些文字性的通信协议，如何变成Servlet/JSP中可用的Java对象，其实就是容器中的剖析与转换。Tomcat就是一种Web容器。

<font color=red>Servlet与JSP/Thymeleaf实际上是一体两面，JSP/Thymeleaf会被Web容器转译为Servlet的.java源代码、编译为.class文件，然后加载容器，因此最后提供服务的还是Servlet实例。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020112810292148.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
上图内容需要背下来。

在Java的Web开发这块，一些重大Web框架，例如SPringle Mvc，仍是基于Servlet，如果能掌握Servlet，在使用这类框架时，对理解底层细节或者进行框架细节控制会有很大帮助。

MVC：model、view、controller---模型、视图、控制器
将MVC概念应用到Web应用程序的设计上来：
（1）视图部分可由网页来实现
（2）服务器上的数据访问或业务逻辑由模型负责
（3）控制器接受浏览器的请求，决定调用哪些模型来处理


Servlet/JSP属于Java EE的范畴。

## 第二章：
Protected void doGet(HttpServletRequest request, 
HttpServletReponse reponse)
{
	//todo
}
<font color=red>在doGet()方法上可以看到HttpServletRequest与HttpServletReponse两个参数，容器接收到浏览器的HTTP请求后，会收集HTTP请求中的信息，并分别创建代表请求与响应的Java对象，而后在调用doGet()方法时将这两个对象作为参数传入。可以从HttpServletRequest对象中取得有关HTTP请求的相关信息，在范例中是通过HttpServletRequest的getParameter()并指定请求参数名称，来取得用户发送的请求参数值。（Spring中的控制器方法与之类似）
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128103052211.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
与Servlet定义相关的类或接口都位于javax.servlet包中，如Servlet、GenericServlet、ServletRequest、ServletReponse等。而与HTTP定义相关的类或接口都位于javax.servlet.http包中，如HttpServlet、HttpServletRequest、HttpServletReponse等。

当应用程序启动后，并没有创建所有的Servlet实例。容器会在首次接到请求需要某个Servlet服务时，才将对应的Servlet类实例化、进行初始化操作，接着再处理请求。这意味着第一次请求该Servlet的浏览器，必须等待Servlet类实例化、进行初始化动作之后，才真正得到请求的处理。

## 第三章：
### 1.从容器到HttpServlet（<font color=red>重点</font>）
当从浏览器发出的请求来到HTTP服务器，而HTTP服务器转交请求给容器时，容器会创建一个代表当前请求的HttpServletRequest对象，并将请求相关信息设置给该对象。同时，容器会创建一个HttpServletResponse对象，作为稍后要对浏览器进行响应的Java对象，如下图所示：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020112810341848.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
如果查询HttpServletRequest、HttpServletResponse的API文档，会发现它们都是接口（interface），而实现这些接口的相关实现类就是由容器提供的。还记得吗？Web容器本身就是一个Java编写的应用程序。
	接着，容器会根据@WebServlet标注或web.xml的设置，找出处理该请求的Servlet，调用它的service()方法，将创建的HttpServletRequest对象、HttpServletResponse对象传入作为参数，service()方法中会根据HTTP请求的方式，调用对应的doXXX()方法。例如，若为GET，则调用doGet()方法。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020112810345843.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128103528890.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020112810360269.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

### 2.关于HttpServletRequest
处理请求参数：请求来到服务器时，Web容器会创建HttpServletRequest实例来包装请求中的相关信息，HttpServletRequest定义了取得请求信息的方法。如：可以使用一下方法取得请求参数：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128103633963.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128103705778.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
HttpServletRequest定义有**getReader()**方法，可以取得一个**BufferedReader**，通过该对象可以读取请求的Body数据。
**getPart()**、**getParts()**取得上传文件：
从上一节使用getInputStream()处理文件上传相关事宜中可以看出，处理过程比较琐碎，在Servlet3.0中，新增了**Part**接口，可以方便的进行文件上传处理。可以通过HttpServletRequest的getPart()方法取得Part实现对象。

使用RequestDispatcher调派请求：
1.使用include()方法：RequestDispatcher的include()方法，可以将另一个Servlet的操作流程包括至目前Servlet操作流程之中。
2.在include()或forward()时包括请求参数的做法，<font color=red>仅适用于传递字符串值给另一个Servlet，在调派请求的过程中，如果有必须共享的“对象”，可以设置给请求对象成为属性，称为<font color=gree>请求范围属性</font>（在自己的项目里用的最多的技术点：且它是HttpServletRequest类对象所具有的方法，HttpServletReponse是不具有该方法的）</font>。---》上面说include()或forward()方法仅使用于传递字符串，所以如果想要传递对象，则可以将对象设置为请求范围属性，以供不同的servlet相互协同工作，前面说过了，前端（JSP/Thymeleaf）实际上也是servlet，所以我们在后端往往可以将对象设置为请求范围属性，然后前端从该属性拿值并显示在前端页面上。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128103933399.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

3.RequestDispatcher有个forward()方法，调用时同样传入请求与响应对象，这表示要将请求处理转发给别的Servlet，“对浏览器的响应同时转发给另一个Servlet”

### 3.关于HttpServletResponse
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128104050755.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128104100556.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128104107825.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
**使用getWriter()输出字符：**
1.	设置Locale
获取或设置相应的地区信息。
2.使用setCharacterEncoding()或setConentType()
设置编码方式（如：UTF-8）

**使用getOutputStream()输出二进制字符**

**使用sendRedirect()、sendError()：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128104214407.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
## 第四章：
Web应用程序的请求与相应是基于HTTP，为<font color=red>无状态的通信协议</font>，服务器不会“记得”这次请求与下一次请求之间的联系。而有些功能必须由多次请求来完成，如：购物车等，这种记得此次请求与之后请求间关系的方式，就称为会话管理（session management）

### 1.先介绍几个实现会话管理的基本方式
**（1）隐藏域（并不是Servlet/JSP实际管理会话时的机制）**
隐藏域不是Servlet/JSP实际管理会话时的机制，此处讲解隐藏域，只是为了说明，由浏览器主动告诉必要的信息，为实现Web应用程序会话管理的基本原理。
**（2）Cookie**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128104349414.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128104357896.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
**（3）URI重写**


### 2.HttpSession会话管理
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128104449933.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128104458461.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
如果想在会话期间让目前的HttpSession失效，可以执行HttpSession的**invalidate()**方法，一个使用的时机就是实现注销机制，调用该方法后，容器会销毁回收HttpSession。

基于Web安全考虑，建议在登录成功后改变SessionID，可以通过HttpServletRequest上新增的**changeSessionId()**来达到。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201128104555790.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

HttpSession默认失效时间，也可以通过ServletContext的setSessionTimeOut()来设定。


## 第五章：
### 1.Servlet进阶API
ServletConfig相当于个别Servlet的设置信息代表对象，这意味着可以从ServletConfig取得Servlet设置信息。ServletConfig定义了getInitParameter()、getInitParameterNames()方法，可以取得设置Servlet时的初始参数。

ServletContext接口定义了运行Servlet的应用程序环境的一些行为与观点，可以使用ServletContext实现对象取得所请求资源的URL、设置与存储属性、应用程序初始参数，甚至动态设置Servlet实例。当整个Web应用程序加载Web容器后，容器会生成一个ServletContext对象，作为整个应用程序的代表，并设置给ServletConfig，只要通过ServletConfig的getServletContext()方法就可以取得ServletContext对象。

ServletContext对象常见方法：
1.getRequestDispatcher()
用于获取RequestDispatcher实例。
2.getResourcePaths()
如果想知道Web应用程序的某个目录中有哪些文件，则可以使用getResourcePaths()方法，它会传回Set<String>实例，包含了指定文件夹中的文件。

3.getResourceAsStream()
该方法用于读取Web应用程序中某个文件的内容。

### 2.监听器
监听器的作用：即当HttpServletRequest对象、HttpSession对象、ServletContext对象在生成、销毁或相关属性设置发生的时机点有兴趣，可以实现对应的监听器，在对应的时机点发生时，Web容器就会调用监听器上相应的方法，让用户在对应的时机点做些什么。

### 3.过滤器
作用：用于拦截浏览器对Servlet的请求，也可以改变Servlet对浏览器的响应


### Spring核心：
@Configuration标注的类，在通过Spring的处理后，在角色上真的就如配置文件，在某些行为上并不是Java程序该有的行为。
@Bean标注告诉Spring，该方法放回的实例会作为bean组件。
@ComponentScan标注，使用了该标注，Spring会自动扫描同一包或其子包下，是否有Bean组件的存在。
@Component标注的类也会被Spring作为bean进行管理
@Autowired注解：实现依赖注入的注解


Spring的控制器没有继承任何类或操作任接口，但是控制器方法的参数可以有HttpServletRequest、HttpServletResponse，这是因为Spring MVC会管理相关的Servlet对象，若发现控制器的方法上有HttpServletRequest、HttpServletResponse对应的类型，在调用时就会自动注入。

参看文献：《servlet&jsp学习笔记》-林信良