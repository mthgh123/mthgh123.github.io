---
title: SpringMVC学习笔记
date: 2021-03-21 21:32:01
categories:
- SpringMVC学习
tags:
- SpringMVC
---

![](http://qiniusave.codeyu.cn/springmvc.jpg)

<!--less-->

---

## SpringMVC

ssm：mybatis + Spring + SpringMVC   ：**MVC三层架构**

javaSE：认真学习，老师带，入门快

javaWeb：认真学习，老师带，入门快

SSM框架：研究官方文档，锻炼自学能力，锻炼笔记能力，锻炼项目能力



<font color='red'>**SpringMVC中有一个十分重要的部分：SpringMVC的执行流程！在面试的时候其重要性堪比面试官问：spring 的IOC和AOP。**</font>

什么是SpringMVC的执行流程呢？

<font color='blue'>即解释：SpringMVC是怎么运行的，为什么发了一个请求它能跳转过去，内部机制，涉及到的类有哪些！</font>

MVC：模型（dao,service）、视图（html/jsp）、控制器（servlet/controller）

<font color='red'>MVC主要作用是降低了视图与业务逻辑间的双向耦合。</font>

<font color='blue'>MVC不是一种设计模式，而是一种架构模式！</font>

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210321200755579.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




<font color='red'>**MVC框架要做的事情：**</font>

1.将url映射到java类或java类的方法

2.封装用户提交的数据

3.处理请求--调用相关的业务处理--封装响应数据

4.将响应的数据进行渲染.jsp/html等视图层数据



## 什么是SpringMVC

SpringMVC是Spring Framework的一部分，也就是Spring的一部分！



为什么要学SpringMVC？或者说SpringMVC有何特点？

1.轻量级，简单易学

2.高效，基于请求响应的MVC框架

3.与Spring兼容性好，无缝结合

4.约定大约配置

5.功能强大：Restful、数据验证、格式化、本地化、主题等

6.简洁灵活

<font color='blue'>spring的web框架围绕DispatcherServlet设计！</font>

<font color='red'>**并且由于SpringMVC是Spring的一部分，所以可以将SpringMVC中所有要用到的bean注册到Spring中！**</font>



<font color='red'>**从下图中可看出：DispatcherServlet仍然是实现了Servlet接口，所以DispatcherServlet本质上也是一个Servlet。**</font>

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210321200832713.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)



SpringMVC原理图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210321200843207.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




<font color='red'>**DispatcherServlet所起到的作用：**</font>

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210321200858695.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


<font color='red'>上图中实线的地方都是SpringMVC帮我们做的，而虚线的地方才是需要我们去做的，所以需要我们去做的内容并不多！</font>

<font color='red'>上面的虚线部分是需要我们做的，但是如果我们把实线部分也了解了，也是就了解DispatcherServlet调度的一系列过程，这就是面试的谈资，很加分！</font>

### <font color='red'>简要分析执行流程：</font>

1.DispatcherServlet表示前置控制器，是整个SpringMVC的控制中心。用户发出请求，DispatcherServlet接收请求并拦截请求：

- 我们假设请求的url为：http://localhost:8080/SpringMVC/hello
- 如上url拆分成三部分：
- http://localhost:8080 服务器域名
- SpringMVC部署在服务器上的web站点
- hello表示控制器
- 通过分析，如上url表示为：请求位于服务器localhost:8080上的SpringMVC站点的hello控制器

2.HandlerMapping为处理器映射器。DispatcherServlet调用！

HandlerMapping，HandlerMapping根据请求url查找Handler。

3.HandlerExecution表示具体的Handler，其主要作用是根据url查找控制器，如上url被查找控制器为：hello。

4.HandlerExecution将解析后的信息传递给DispatcherServlet，如解析控制器映射等。

5.HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler。

6.Handler让具体的Controller执行。



如果控制器类上标注的是@Controller且控制器方法上没有标注@ResponseBody，那么返回的字符串将会被解析为视图：

```java
@Controller
public class HelloController {
    @GetMapping("/hello")
    public String hello(){
        return "hello";
    }
}
```

如果控制器类上标注的是@RestController，则返回的字符串数据将不会被解析为视图，而是以json字符串的形式返回字符串。



### 使用SpringMVC必须配置的三大件

**处理器映射器、处理器适配器、视图解析器**

通常，我们只需要手动配置**视图解析器**，而**处理器映射器**和**处理器适配器**只需要开启**注解驱动**即可，而省去了大段地xml配置！



### Controller接口

<font color='red'>**实现了该接口的类将获得控制器功能！**</font><font color='blue'>但是，实现Controller接口的方式是一种较老的办法，一般不这么做。</font>**因为这种方法有一个缺陷，即一个控制器类中只能写一个控制器方法！（重写handleRequest()方法）**，所以一般使用注解的方式！

该接口位于org.springframework.web.servlet.mvc包下，接口只有一个方法：

```java
@FunctionalInterface
public interface Controller {

	/**
	 * Process the request and return a ModelAndView object which the DispatcherServlet
	 * will render. A {@code null} return value is not an error: it indicates that
	 * this object completed request processing itself and that there is therefore no
	 * ModelAndView to render.
	 * @param request current HTTP request
	 * @param response current HTTP response
	 * @return a ModelAndView to render, or {@code null} if handled directly
	 * @throws Exception in case of errors
	 */
	@Nullable
	ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception;
}
```



## Restful风格

**概念：**

Restful就是一个资源定位及资源操作的风格。不是标准也不是协议，只是一种风格。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。



简单理解Restful风格就是将参数加入到路径中，而不作为?后的参数进行传递，如：

非Restful风格：localhost:8080/method?add=1

Restful风格：localhost:8080/method/1

 

<font color='red'>Restful风格特点：简洁，高效（支持缓存），安全！</font>

Restful风格的一个好处：安全，减少了暴露的参数字段！





## 重定向与转发

原生态的重定向与转发为通过下方代码实现：

```java
//HttpServletRequesst req, /HttpServletResponse resp
//转发操作
req.setAttribute("msg","/result/t3");
req.getRequestDispatcher("/WEB-INF/jsp/index.html");
```

简化后的重定向与转发为：

```java
return "froward:/index.html";
//或
return "redirect:/index.html";
```



## 过滤器

```xml
<!--配置SpringMvc的乱码过滤，在web.xml中配置了这个之后就不用写实现了Filter接口的过滤器相关的类和方法了，因为配置SpringMvc帮我们做了.只用进行下方的这些配置，即可实现过滤解决乱码问题！-->
<filter>
    <filter-name>encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern> <!---注意：此处是/*->
</filter-mapping>
```



## Controller返回JSON数据

- Jackson应该是目前比较好的json解析工具了！

- 当然工具不止这一个，还比如阿里巴巴的fastjson等扽

- 我们这里使用Jackson，其依赖为：

  ```xml
  <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
  <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.12.2</version>
  </dependency>
  ```



### Jackson中的ObjectMapper

```java
ObjectMapper mapper = new ObjectMapper();
//创建一个对象
User user = new User("吃吃",3,"女");
//通过ObjectMapper的方法将实体类转化为字符串
String str = mapper.writeValueAsString(user); //这个str就是json字符串
return str;
```



### 乱码问题解决

<font color='red'>如果使用ObjectMapper将实体类转化为json字符串后出现乱码问题，那么需要将其编码格式设置为utf-8：通过@RequestMapping的produces属性来实现！</font>

```java
//produces指定响应体返回类型和编码
@RequestMapping(value="/json1", produces="application/json;charset=utf-8")
```

但上面这种方式比较麻烦，SpringMVC有一种乱码统一的解决方式，如下：

我们可以在springmvc的配置文件上添加一段消息StringHttpMessageConvert转换配置！

```xml
<!--在ssm框架中，只要使用Jackson，就将下方的内容配置到配置文件中去，以解决乱码问题！-->

<!--JSON乱码问题配置-->
<mvc:annotation-driven>
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <constructor-arg value="UTF-8"/>
        </bean>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
            <property name="ObjectMapper">
                <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                    <property name="failOnEmptyBeans" value="false"/>
                </bean>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>

```



### 不以时间戳的格式输出Date数据

ObjectMapper默认以时间戳的方式打印输出时间！

```java
ObjectMapper mapper = new ObjectMapper();
//关闭日期的时间戳输出方式
mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);
//自定义日期格式对象
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
//指定日期格式
mapper.setDateFormat(sdf);

Date date = new Date();
String str = mapper.writeValueAsString(date);
return str;
```



## 拦截器

SpringMVC的处理器拦截器类似于Servlet开发中的Filter，用于对处理器进行预处理和后处理。开发者可以自己定义一些拦截器来实现特定的功能！

过滤器与拦截器的区别：<font color='red'>拦截器是AOP思想的具体应用！</font>

**过滤器（Filter）：**

- servlet规范中的一部分，任何java web工程都可以使用
- 在url-pattern中配置了/*之后，可以对所有要访问的资源进行拦截

**拦截器（Interceptor）:**

- 拦截器是SpringMVC框架自己的，只有使用了SpringMVC框架的工程才能使用
- 拦截器只会拦截访问的控制器（controller）方法，如果访问的是jsp/html/css/image/js是不会进行拦截的



### 自定义拦截器

如何自定义拦截器？

<font color='red'>**答：实现HandlerInterceptor接口即可！**</font>

```java
//return true; 执行下一个拦截器，放行。返回false，则拦截，不予放行
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    System.out.println("=======处理前=======");
    return true;
}
```



<font color='blue'>/**代表过滤所有东西，/\*代表过滤当前文件夹下的东西！</font>

<font color='blue'>而如果是/admin/**，则代表拦截/admin路径下的所有东西！</font>

```xml
<!--拦截器配置-->
<mvc:interceptors>
    <mvc:interceptor>
        <!--包括这个请求下面的所有请求-->
        <mvc:mapping path="/**"/>
        <bean class="com.codeyu.interceptor.MyInterceptor"></bean>
    </mvc:interceptor>
</mvc:interceptors>
```



## 总结和展望

 springmvc就一个重点，即 springmvc的执行流程！（面试常问的点） 

### SSM框架所涉及到的内容：

mybatis：

- **ResultMap结果集映射（重点）（一对多/多对一）**
- Log4j
- 分页
- 注解开发
- 动态SQL
- 缓存



spring：

- IOC理论
- Spring概述
- IOC-Beans.xml
- **DI-Set/cp（重点）**
- **代理模式（静态，动态）：重点，问的很多，比如会问动态代理的实现机制是什么**
- AOP
- 注解开发
- **JavaConfig（半重点）**
- **整合Mybatis：事务（重点）**



springmvc：

- **springmvc执行流程（重点）**
- 回顾MVC
- HelloSpringMVC
- 执行原理
- 结果跳转的方式
- 数据如何处理
- 乱码问题
- Controller
- **RestFul（重点）**
- **整合SSM框架（重点）**
- JSON
- Ajax
- 拦截器





