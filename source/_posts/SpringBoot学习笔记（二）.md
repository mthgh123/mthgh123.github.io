---
title: SpringBoot学习笔记（二）
date: 2021-01-27 21:06:03
categories:
- SpringBoot学习
tags:
- SpringBoot
---

![](http://qiniusave.codeyu.cn/springboot.jpg)

<!--less-->

---

## SpringBoot Web开发---关于这一块的源码部分，主要看WebMvcAutoConfiguration类

jar：webapp！

<font color='orange'>在利用SpringBoot进行开发时，始终需要思考的四个问题：springboot到底帮我们配置了什么？我们能不能进行修改？能修改哪些东西？能不能扩展？</font>

- xxxAutoConfiguration：向容器中自动配置组件
- xxxProperties：自动配置类，装配配置文件中自定义的一些东西！



### 要解决的问题：

- 导入静态资源
- 首页
- jsp，模板引擎Thymeleaf
- 装配扩展SpringMVC
- 增删改查
- 拦截器
- 国际化！



## 静态资源

静态资源的获取，下方讲解的两种方式，归根到底就是下方的源码部分，搞懂源码部分也就啥都懂了，源码部分在WebMvcAutoConfiguration配置类中：

```java
//spring.factories文件中的WebMvcAutoConfiguration配置类的内部类EnableWebMvcConfiguration的一个方法：addResourceHandlers()方法定义如下：
@Override
protected void addResourceHandlers(ResourceHandlerRegistry registry) {
   super.addResourceHandlers(registry);
    
   //这段if代码：如果在properties/yaml文件中自定义了静态资源相关属性，则自定义的生效，默认的无效，故直接返回
   if (!this.resourceProperties.isAddMappings()) {
      logger.debug("Default resource handling disabled");
      return;
   }
   ServletContext servletContext = getServletContext();
   
   //方式1：此处的含义就是你知道访问路径/webjars/**就可以访问到classpath:/META-INF/resources/webjars/下的文件
   addResourceHandler(registry, "/webjars/**", "classpath:/META-INF/resources/webjars/");
    
   //方式2：通过获取静态资源的路径来使用静态资源，this.mvcProperties.getStaticPathPattern()最终定位到"/**"，即当前目录下的所有资源都会识别。而this.resourceProperties.getStaticLocations()会定位到{ "classpath:/META-INF/resources/","classpath:/resources/", "classpath:/static/", "classpath:/public/" }，所有springboot项目会自动去相应的路径下寻找静态资源
   addResourceHandler(registry, this.mvcProperties.getStaticPathPattern(), (registration) -> {
      registration.addResourceLocations(this.resourceProperties.getStaticLocations());
      if (servletContext != null) {
         registration.addResourceLocations(new ServletContextResource(servletContext, SERVLET_LOCATION));
      }
   });
}
```

### 拿到静态资源的方式有多种：

#### 1.去webjars网站（链接：https://www.webjars.org/）上寻找，也可以去mvcrepository网站上去寻找静态资源，然后通过pom文件中加入依赖从而将静态资源下载到本地来（但平时一般不这么使用）：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210223170423883.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/2021022317044219.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




#### 2.通过路径来使用静态资源

<font color='red'>结合源代码的分析：我们可以通过访问`/**`来访问后面四个路径下的静态资源，也就是当我们访问http://localhost:8080/**下的任何资源，那么项目就会去{ "classpath:/META-INF/resources/","classpath:/resources/", "classpath:/static/", "classpath:/public/" }这些路径下访问静态资源！</font>举例：比如classpath:/resources/目录中有一个1.js文件，那么启动项目后，我们可以在浏览器中输入http://localhost:8080/1.js来访问classpath:/resources/目录中的1.js文件。

三个目录的优先级：classpath:/resources/最高，其次classpath:/static/，最后classpath:/public/，所以一般我们在public中放一些公共的资源，static中放一些静态资源，比如说图片等等，resources中则放一些上传的文件！



静态资源的路径如下图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210223170508211.png#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/20210223170525838.png#pic_center)




使上方路径失效的方式：即定义自己的静态资源访问路径，如下图所示：

```java
   //这段if代码：如果在properties/yaml文件中自定义了静态资源相关属性，则自定义的生效，默认的无效，故直接返回
   if (!this.resourceProperties.isAddMappings()) {
      logger.debug("Default resource handling disabled");
      return;
   }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210223170552162.png#pic_center)




### 总结

1.在springboo，可以使用以下方式处理静态资源

- webjars
- public、static、resources           //那么，在localhost:8080/路径下即可访问	public、static、resources目录中的资源了



2.优先级：resources>static（默认）>publi c



<font color='orange'>一般不推荐使用webjars，而是将静态资源放到static中去！</font>





## 首页如何定制

源码部分，还是WebMvcAutoConfiguration类中：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021022317061559.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210223170649184.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/20210223170725186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


<font color='red'>通过上方的源码分析也就知道了，首页可以自行配置并指定，如果不自行配置并指定的话，可以在静态资源目录下新建一个index,html文件，springboot会默认自动将其作为首页！</font><font color='blue'>但是如果将index.html放在templates目录下的话，则只能通过Controller方法来访问，springboot不会自动将其作为首页！（但前提是需要在pom中加入模板引擎的依赖，并在html文件最前面加入对应模板引擎头）</font>

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210223170759554.png#pic_center)


<font color='orange'> 在templates目录下的所有页面只能通过controller来跳转！但前提是需要在pom中加入模板引擎的依赖，并在html文件最前面加入对应模板引擎头</font>



## 模板引擎

结论：若需要使用Thymeleaf作为模板引擎，只需要在pom.xml中引入依赖即可！并且将我们的html页面放在templates目录下！

<font color='red'>还有值得注意的一点就是：当Controller方法上标注了@ResponseBody并且方法返回String类型时，那么该方法就会返回一个字符串，而不会跳转到对应的视图，但是如果不加@ResponseBody注解并且方法返回String类型时，则会跳转到对应的视图</font>

## 扩展MVC

学习webMVC的官网资料有：https://docs.spring.io/spring-framework/docs/5.3.4/reference/html/web.html#mvc

springboot下的springMVC：https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/spring-boot-features.html#boot-features-developing-web-applications   官网说：If you want to keep those Spring Boot MVC customizations and make more [MVC customizations](https://docs.spring.io/spring/docs/5.3.4/reference/html/web.html#mvc) (interceptors, formatters, view controllers, and other features), you can add your own `@Configuration` class of type `WebMvcConfigurer` but **without** `@EnableWebMvc`.<font color='red'>（如果想保持springboot MVC的自定义并实现扩展，那么我们可以添加标注了`@Configuration` 的`WebMvcConfigurer`类型的自动配置类，但是不要标注`@EnableWebMvc`注解）`WebMvcConfigurer`是一个接口</font>

### 实现MVC扩展分三步：

- 配上标注@Configuration注解
- 类上不要标注@EnableWebMvc注解（因为一旦标注了它，MVC会全面接管！并且自动配置会无效！）
- 实现WebMvcConfigurer接口

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210223170830962.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


拿重写addInterceptors()方法举例：

<font color='red'>原来配置拦截器的话就是实现拦截器的接口，现在配置一个自动配置类重写里面的addInterceptors()方法就可以了！</font>



### 了解SpringMvc的相关类

#### 1.ContentNegotiatingViewResolver

该类实现了`ViewResolver`接口，因为实现了`ViewResolver`接口的类我们可以称之为视图解析器，所以`ContentNegotiatingViewResolver`类是视图解析器。

<font color='red'>但是这个视图解析器是springboot自动帮我们装配的，那么我们如何手动的去接管它呢？即定义自己的视图解析器？</font>

<font color='blue'>定义自己的视图解析器：1.只需要自己写一个视图解析器，即实现`ViewResolver`接口，2.然后把它注册到Bean之中。那么springboot就会自动帮我们装配上。</font>

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210223170858613.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


#### 结论：

如果想要自定义一些功能，只要在MVC扩展类的基础上写一些组件即可，然后将它交给springboot，springboot会帮我们自动装配！



#### 2.自定义配置格式化

在`WebMvcAutoConfiguration`配置类中，有下面一个方法：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210223170916250.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


总共有下图所示的三种格式的配置方式<font color='cornflowerblue'>：这是因为format类本身含有data,time,datetime三个字段</font>：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210223170932589.png#pic_center)




#### 结论：

SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自己配置的（如果用户自己配置@bean），如果有就用用户配置的，如果没有就用自动配置的；如果有些组件可以存在多个，比如我们的视图解析器，就将用户配置的和自己默认的组合起来！







## 国际化

分三步：

- 我们需要配置i18n文件
- 我们如果需要在项目中进行按钮自动切换，我们需要自定义一个实现了`LocaleResolver`接口的类
- 记得将自己写的类注册到spring容器中去，即@Bean







## 拦截器

实现拦截器分两步：

- 定义拦截器类，即实现拦截器接口即可，并且一般情况下只用重写preHandle()方法
- 在实现了WebMvcConfigurer接口的配置类中重写addInterceptors()方法，并配置拦截哪些页面放行哪些页面！



## 404以及500

404在springboot项目中非常的容易，只需要在templates文件夹下新建一个error文件夹，并在该文件夹下新建一个404.html/500.html文件即可！



## 如何快速搭建一个web应用

1.前端搞定：页面长什么样子，这样才能知道需要哪些数据，从而方便设计数据库！

2.设计数据库！

3.前端让它能够自动运行，独立化工程！（即前后端分离）

4.数据接口如何对接：json

5.前后端联调测试！

<font color='red'>以上的5步，难点在哪个地方：数据库的设计是难点！</font>

<font color='blue'>建议：1.可以学习一个前端模板（可以到bootstrap网站/x-admin网站寻找！）2.前端界面：至少自己能够通过前端框架，组合出来一个网站页面！3.让这个网站独立运行起来！</font>