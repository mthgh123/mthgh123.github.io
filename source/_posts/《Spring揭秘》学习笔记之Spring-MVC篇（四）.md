---
title: 《Spring揭秘》学习笔记之Spring MVC篇（四）
date: 2020-12-07 20:22:48
categories:
- 《Spring揭秘》学习笔记
tags:
- Spring
---

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207203429526.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<!--less-->

## 概述

该部分为学习《Spring揭秘》后第26章的学习笔记，主要是Spring MVC部分的内容，这也是spring MVC系列篇博客的收官之作。

## 第26章：
### 26.1 初识基于注解的Controller
在Spring MVC框架中，传统的Handler类型，比如Controller或者ThrowawayController，都需要具体实现类继承某个基类或者实现某个接口。而使用基于注解的Controller的话，则没有这样的限制。（spring boot中就是这样的）

传统的Controller或者ThrowawayController通过接口类型作为标志的方式，基于注解的Controller则采用标注于具体实现类上的某种类型的注解作为标志，比如使用：@Controller和@RequestMapping两种类型的注解来标注该类。

标注了相应的注解并启动项目，通过@ComponentScan对相应包进行扫描之后，剩下的事情，像如何获取并调用这些基本注解的Controller等，就由Spring MVC框架帮我们全部揽下来了。<font color=oranage>对于下方图所示的：/helloAnnoController.anno形式的请求，Spring MVC将使用AnyTypeYouLikeController作为处理请求的Handler，并调用其标注了@RequestMapping的processWebRequest()方法进行当前Web请求的处理。</font>
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207191402704.png#pic_center)


### 26.2 基于注解的Controller原型分析
需要搞清两个问题：
1.如何让Spring MVC框架类（其实就是DispatcherServlet）知道当前Web请求应该由哪个基于主机的Controller处理？
2.如何让Spring MVC框架类知道调用基于注解的Controller的哪个方法来处理具体的Web请求？

26.2.1 自定义用于基于注解的Controller的HandlerMapping
在基于注解的Controller中，当前实现类将用于哪个Web请求的处理是由相应的注解标注的。比如，@RequestMapping(“/helloAnnotation.anno”)标注的AnyTypeYouLikeController将用于处理URL为“/helloAnnotation.anno”的Web请求。<font color=red>这些注解中的信息，我们需要通过Java的反射机制来读取。</font>
所以，我们不得不为基于注解的Controller提供特定的HandlerMapping实现。<font color=oranage>**其基本实现思想是：遍历所有可用的基于注解的Controller实现类，然后根据web请求的路径信息，与实现类中的注解所标注的请求处理映射信息进行对比。如果当前基于注解的Controller实现类的注解所包含的信息与web请求的路径信息相匹配，那么就返回当前这一基于注解的Controller实现类即可。**</font>Spring 2.5中基于注解的Controller依赖于官方给出的HandlerMapping接口实现类：org.springframework.web.servlet.mvc.annotation.Defa-ultAnnotationHandlerMapping。其主要方法getHandler()的实现原理与我们上述的基本实现思想几乎差不多：它会首先扫描应用程序的Classpath，通过反射获取所有标注了@Controller的对象，当web请求到来后，通过实现了上述思路的getHandler()方法获取对应得handler。

#### 26.2.2 自定义用于基于注解得Controller得HandlerAdaptor
HandlerMapping返回了处理Web请求得某个基于注解得Controller实例。而DispatcherServlet并不知道应该去调用该实例的哪个方法来处理Web请求。为了能够以统一的方式调用各种类型的Handler，DispatcherServlet需要一个针对基于注解的Controller的HandlerAdaptor实现。与DefaultAnnotationHandlerMapping能够使用反射获取相应注解信息这一做法类似，针对基于注解的Controller的HandlerAdaptor原型实现，同样可以如法炮制，只要通过反射查找标注了@RequestMapping的方法定义，然后通过反射调用该方法，并返回DispatcherServlet所需要的ModelAndView即可。

HandlerAdaptor需要解决哪些问题？
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207201039424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207201055240.png#pic_center)


说了这么多，实际上就一个目的，实现一个针对基于注解的Controller的HandlerAdaptor的目的是明确的，<font color=red>**那就是将请求信息绑定到具体的Controller实例**</font>，然后调用相应的处理方法，并将返回结果以ModelAndView的形式返回给DispatcherServlet使用。
Spring 2.5中为基于注解的Controller提供的HandlerAdaptor实现类是org.springframework.web.servlet.mvc.annotation.AnnotationMe-thodHandlerAdaptor。该类为我们考虑了以上提到的几乎所有问题，甚至还要更全面。默认情况下，2.5版本的DispatcherServlet将在初始化的时候就实例化一个AnnotationMethodHandlerAdaptor，用于支持基于注解的Controller。

### 26.3 近看基于注解的Controller
#### 26.3.1 声明基于注解的Controller
1.再谈@Controller
要想使一个普通的对象定义成为Spring MVC框架使用的基于注解的Controller，需要用@Controller这个注解进行标注。
@Controller是基于注解的Controller的身份象征，它主要有如下两个作用：
（1）使用<context:component-scan/>之后，标注了的对象可以被纳入Spring IoC容器进行管理，这样，就可以为其提供依赖注入等服务。（实现在spring的web.xml中标注<context:component-scan/>的功能是在spring boot的启动类上标注：@SpringBootApplication注解，该注释包含@ComponentScan注解的功能）
（2）在DefaultAnnotationHandlerMapping查找对应的Controller以处理当前Web请求的时候，可以将标注了@Controller的对象列入被考虑的范畴。

2.@RequestMapping详解
@RequestMapping可以被标注于类型声明或者方法声明上。如果声明在类型定义上，那么@RequestMapping通常作为请求处理映射信息的提供者。如果声明在方法定义上，那么@RequestMapping更多的是为了表明当前方法定义是一个Web请求处理方法。简单点说就是：标注于类型定义的@RequestMapping提供映射信息，标注在方法定义的@RequestMapping标志具体的请求处理方法。

标注在方法上的@RequestMapping除了提供一个映射信息外，还可以提供额外两个属性，一个是method属性，用于标识接受的web请求方法是get还是post，另一个是params属性。

3.请求处理方法签名规则说明
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207201151575.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207201200195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

 

使用@RequestParam或者@ModelAttribute所标注的方法参数。相应方法参数将根据这两个注解的语义获得相应的对象值。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207201233505.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


#### 26.3.2 请求参数到方法参数的绑定
1.默认绑定行为
默认绑定行为是指根据名称匹配原则进行的数据绑定。当请求中的参数与方法参数名称一致的时候，相应的参数值将被绑定到对应的方法参数上。而一旦名字稍有差异，将会绑定数据失败并产生异常。
2.使用@RequestParam明确地指出绑定关系
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201207201253622.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

默认情况下，如果@RequestParam所指的请求参数不存在，绑定过程中将抛出异常。这是由@RequestParam的required属性决定的，它的默认值为true，要求指定的请求参数必须存在。当然，当指定其为false时，则此参数可以不绑定。

#### 26.3.3 使用@ModelAttribute访问模型数据
Spring 2.5之后，使用@Autowired注解来实现依赖注入，这样就可以避免通过XML配置来完成依赖注入关系的确定。
<font color=red>**如果将@ModelAttribute标注在某个方法上，该方法所返回的数据将添加到模型数据中。（所谓的添加到模型数据中，即添加到ModelMap中，在我自己的springBoot项目中，是通过使用request.setAttribute(“key”,value)方法来存储模型数据的，然后在.html页面直接通过key来取模型数据并显示。即此处的@ModelAttribute注释实现的功能与自己项目中的request.setAttribute(“key”,value)实现的功能是一样的。）**</font>
#### 26.3.4 通过@SessionAttribute管理Session数据
在你自己的springBoot项目中，是通过设定方法参数HttpSession session然后来对session进行管理的。而此处是通过@SessionAttribute来实现对session的管理工作。

参考文献：《Spring揭秘》王福强-著