---
title: SpringBoot学习笔记（一）
date: 2021-01-23 16:02:44
categories:
- SpringBoot学习
tags:
- SpringBoot
---

![](http://qiniusave.codeyu.cn/springboot.jpg)

<!--less-->

---


## 微服务阶段

javase：OOP

mysql：持久化

html+css+js+jquery+框架：视图、框架不熟练

javaWeb：独立开发MVC三层架构的网站

ssm：框架：简化了我们开发的流程，配置也开始比较复杂

到此处为止：上方的这所有的都是打包成java中的war包

Spring再简化：SpringBoot - jar ：内嵌tomcat；微服务架构！

服务越来越多：SpringCloud

---
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150056593.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




### 什么是微服务？

微服务是一种架构风格，它要求我们在开发一个应用时，这个应用必须构建一系列小服务的组合，也就是多模块组合（你在实习过程中遇到的项目都是这样的多模块组合）；可以通过http/rpc等的方式进行互通。要说微服务架构，要说一说过去的单体应用架构！

<font color='red'>一个一个的模块都可以打包成jar包，这些jar包就是相应的接口！</font>

### 单体应用架构？

所谓单体应用架构（all in one）是指，我们将一个应用中的所有应用服务都封装在一个应用中。

无论是ERP、CRM或是其他什么系统，你都把数据库访问，web访问，等等各个功能放到<font color="red">一个war包内</font>。

- 这样做的好处是，易于开发和测试；也十分方便部署；当需要扩展时，只需要将war复制多份，然后放到多个服务器上，再做个负载均衡就可以了。

- 单体应用架构的缺点是，<font color='blue'>哪怕我要修改一个非常小的地方，我都需要停掉整个服务，重新打包、再部署这个应用war包</font>。特别是对于一个大型应用，我们不可能吧所有内容都放在一个应用里面，我们如何维护、如何分工合作都是问题。

### 微服务架构

all in one的架构方式，我们把所有的功能单元放在一个应用里面。然后我们把整个应用部署到服务器上。如果负载能力不行，我们将整个应用进行水平复制，进行扩展，然后在负载均衡。

<font color='red'>所谓微服务架构，就是打破之前all in one的架构方式，把每个功能元素独立出来（和你实习过程中看到项目的一样！）</font>。把独立出来的功能元素的动态组合，需要的功能元素才去拿来组合，需要多一些时可以整合多个功能元素。所以微服务架构是对功能元素进行复制，而没有对整个应用进行复制。

这样做的好处是：

- <font color='orange'>1.节省了调用资源。</font>
- <font color='orange'>2.每个功能元素的服务都是一个可替换的、可独立升级的软件代码。</font>

### 如何构建微服务

一个大型系统的微服务架构，就像一个复杂交织的神经网络，每一个神经元就是一个功能元素，它们各自完成自己的功能，然后通过http相互请求调用。<font color='red'>比如一个电商系统，查缓存、连数据库、浏览页面、结账、支付等服务都是一个个独立的功能服务，都被微化了，它们作为一个个微服务共同构建了一个庞大的系统。如果修改其中的一个功能，只需要更新升级其中一个功能服务单元即可。</font>



但是这种庞大的系统架构给部署和运维带来很大的难度。于是，spring为我们带来了构建大型分布式微服务的全套、全程产品：

- 构建一个个功能独立的微服务应用单元，可以使用springboot，可以帮我们快速构建一个应用；

- 大型分布式网络服务的调用，这部分由spring cloud来完成，实现分布式；

- 在分布式中间，进行流式数据计算、批处理，我们有spring cloud data flow。

- spring为我们想清楚了整个从开始构建应用到大型分布式应用全流程方案。



## 第一个SpringBoot程序

第一个SpringBoot程序的创建并不困难，值得学习的是项目打包成jar包之后可以拿到任何地方去使用，如下所示！

### 1.通过mvn package命令将SpringBoot项目打包成jar包

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150117902.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




### 2.到该jar包的同级目录下通过运行命令行命令来运行该jar包

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150131739.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


### 3.之后通过浏览器访问对应URL网页，发现成功执行！



## 如何在applocation.properties文件中进行相关配置

就拿redis的相关配置来讲解吧！

### 1.在库文件中找到spring-boot-autoconfigure文件

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150150123.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


然后打开它的spring.factores文件。

### 2.找到RedisAutoCinfiguration文件

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150215847.png#pic_center)


按ctrl+左键定位到该配置类。

### 3.找到标有@EnableConfigurationProperties注解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150226203.png#pic_center)


### 4.以spring.redis为前缀，配置相应的属性即可

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150236468.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


## 插件失败了，就再install一下

双击下图install即可：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150306348.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)






## 原理初探

配置原理：



### **pom.xml：**

- spring-boot-dependencies.pom：核心依赖在父工程中！（版本管理就在该.pom文件中）
- 我们在写或者引入一些springBoot依赖的时候，不需要指定版本，就是因为有这些版本库，即spring-boot-dependencies.pom中的版本库



### **启动器：**

- ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
  </dependency>
  ```

- 启动器：说白了就是SpringBoot的启动场景；

- 比如spring-boot-starter-web，它就会帮我们自动导入web环境所有的依赖！

- springboot会将所有的功能场景，都变成一个个的启动器

- 我们要使用什么功能，就只需要找到对应的启动器就可以了 `starter`



### **主程序：**

```java
//@SpringBootApplication：标注这个类是一个springboot的应用
@SpringBootApplication
public class FirstSpringbootApplication {

    public static void main(String[] args) {
        //将springboot应用启动
        SpringApplication.run(FirstSpringbootApplication.class, args);
    }

}
```

#### 1.注解：

- ```java
  @SpringBootConfiguration：springboot的配置
  	@Configuration： spring配置类
  		@Component： 说明这一是一个组件
  
  @EnableAutoConfiguration： 自动配置
  	@AutoConfigurationPackage： 自动配置包
  		@Import({Registrar.class})： 导入注册器
      @Import({AutoConfigurationImportSelector.class})： 自动配置导入选择器
      
  //获取所有的配置
  List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
  ```

获取候选的配置：

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
				getBeanClassLoader());
		Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
		return configurations;
	}
```

```
Properties properties = PropertiesLoaderUtils.loadProperties(resource);
所有的资源加载到配置类中！
```



**结论：**

springboot所有自动配置都是在启动的时候扫描并加载的：`sprnig.factories`中所有的类不一定生效，因为有些类是有@ConditionalOnXXX才会生效，当starter未导入相应的包时，`sprnig.factories`中对应的配置类就不生效。

1. springboot在启动的时候，从类路径下/META-INF/sprnig.factories获取指定的配置类
2. 将这些自动配置的类导入容器（但并不是所有的自动配置类都会被导入容器，因为@ConditionalOnXXX注解，也就是只有通过starter导入了使得@ConditionalOnXXX条件成立时的包时，相应的自动配置类才会被导入容器），自动配置就会生效，帮我们自动配置
3. 以前我们需要配置的东西，现在springboot帮我们做了！
4. 整个javaEE，解决方案和自动配置的东西都在spring-boot-autoconfigure-x.x.x.Release.jar包下
5. 它会把所有需要导入的组件，以类名的方式返回，这些组件都会被添加到容器
6. 容器中也会存在非常多的xxxAutoConfiguration的文件（@Bean），就是这些类给容器中导入了这个场景需要的所有组件；并自动配置，@Configuration
7. 有了自动配置类，免去了我们手动编写配置文件的工作！



#### 2.run方法

```java
public static void main(String[] args) {
        //将springboot应用启动
        SpringApplication.run(FirstSpringbootApplication.class, args);//run方法
    }
```

SpringApplication.run分析：分析该方法主要两部分，一部分是SpringApplication的实例化，二是run方法的执行；



SpringApplication类主要做了以下四件事：

- 1.推断应用的类型是普通的项目还是Web项目
- 2.查找并加载所有可用初始化器，设置到initializers属性中
- 3.找出所有的应用程序监听器，设置到listeners属性中
- 4.推断并设置main方法的定义类，找到运行的主类

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150350218.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


面试时如果遇到问题：关于SpringBoot，谈谈你的理解：

- 自动化装配

  ```
  全面接管SpringMVC的配置！
  ```

  

- run()--->就说这四步

  ```
  - 1.推断应用的类型是普通的项目还是Web项目
  - 2.查找并加载所有可用初始化器，设置到initializers属性中
  - 3.找出所有的应用程序监听器，设置到listeners属性中
  - 4.推断并设置main方法的定义类，找到运行的主类
  ```





## SpringBoot配置

SpringBoot使用一个全局的配置文件，配置文件名称是固定的

- application.properties
  - 语法结构：key=value
- application.yaml
  - 语法结构：key: 空格 value

<font color='orange'>配置文件的作用：</font>修改SpringBoot自动配置的默认值，因为SpringBoot在底层都给我们自动配置好了；



### yaml配置

**<font color='red'>yaml可以直接给实体类赋值！</font>**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150412956.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150422994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


使用yaml的配置方式如下图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150439633.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


**<font color='orange'>以后你会发现，很多<font color='blue'>配置类</font>都会通过yaml去赋值，比如redis、templates等</font>**

**@ConfigurationProperties作用：**

- 将配置文件中配置的属性的值，映射到这个组件中；
- 告诉springBoot将本类中的所有属性和配置文件中相关的配置项进行绑定
- 参数prefix=“person"：与配置文件中的person下面的所有属性一一对应
- 只有这个组件是容器中的组件，才能使用容器提供的@ConfigurationProperties功能



### .properties配置

当然，实际上.properties文件也是可以实现与yaml文件一样的作用，如下图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150506409.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




### 综上：

给配置类/实体类中属性赋值的方式有：

1. @Value注解
2. 通过yaml配置文件赋值（<font color='orange'>推荐这种方式</font>）
3. 通过properties配置文件赋值



使用基于@ConfigurationProperties(prefix = "xxx")注解的yaml配置方法与基于@Value注解的properties配置方法比较： 

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021022215052448.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




### 结论

- 配置yaml和配置properties都可以获取到值，强烈推荐yml
- 如果我们在某个业务中，只需要获取配置文件中的某个值，可以使用以下@Value
- 如果说，我们专门编写了一个JavaBean来和配置文件进行映射，就可以直接使用@ConfigurationProperties，不要犹豫！



## JSR303校验

该校验使用的注解有@Validated，还有与该注解配置使用的注解：

```java
@NotNull(message="，名字不能为空")
private String username;
@Max(value=120,message="年龄最大不能超过120")
private int age;
@Email(message="邮件格式错误")
private String email;

//空检查
@Null		//验证对象是否为null
@NotNull	//验证对象是否不为null，无法检查长度为0的字符串
@NotBlank	//检查约束字符串是不是null以及被Trim的长度是否大于0，只对字符串，且会去掉前后空格
@NotEmpty	//检查约束元素是否为null或者empty

//Boolean检查
@AssertTrue		//验证Boolean对象是否为true
@AssertFalse	//验证Boolean对象是否为false

//长度检查
@Size(min= , max= )	//验证对象（Array,Collection,Map,String）长度是否在给定的范围之内
@Length(min= ,max= )//验证被注解的字符串的长度是否在min和max之间

//日期检查
@Past		//验证Date和Calendar对象是否在当前时间之前
@Future		//验证Date和Calendar对象是否在当前时间之后
@Pattern	//验证String对象是否符合正则表达式的规则

等等...
除此之外，我们还可以自定义一些数据校验规则
```

源码位置：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150555305.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)






更过见链接：https://www.jianshu.com/p/554533f88370

需要在pom.xml中引入依赖项：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```



### 测试：

通过@Validated以及@Email注解要求获取的字段必须是邮件格式，但是在yml中给定的字段不是邮件格式，使得测试运行报错：



![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150759300.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150911588.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




## 多环境配置以及配置文件位置：

可以在哪些地方写配置文件：路径`file`指的是工程的根目录

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222150932310.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


<font color='red'>查找这四个配置文件的顺序是如上图的1234所示！假设1234都有相同的配置项，那么1中的配置项会覆盖234中的配置项</font>



- 路径`file:./config`指的是

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222151001530.png#pic_center)


- 路径`file:./`指的是

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222151020389.png#pic_center)


- 路径`classpath:/config/`指的是

  所谓类路径（classpath）：指的是java或resource这两路

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222151041690.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


所以`classpath:/config/`指的是

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222151059520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


- 路径`classpath:/`指的就是默认生成的那个applicaiton.properties了



### .properties文件实现不同开发环境的配置

<font color='red'>现实开发中，可能针对不同的环境，比如测试环境/开发环境，会有多个配置文件：</font>
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021022215114764.png#pic_center)


激活dev开发环境，只用如下方所示写法即可，test开发环境的话则换成test即可，在默认的配置文件application.properties中配置需要激活的配置文件：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222151204795.png#pic_center)


比如：test环境与dev环境用的数据库不同，那么肯定就要使用不同的数据库配置嘛！



### yaml文件实现不同开发环境的配置

yaml使用一个文件即可实现上述的三个文件的作用：通过<font color='orange'>多文档模块</font>，即通过三个横杠符号来实现！`---`

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021022215122319.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




## 自动配置原理再理解

### .properties/.yaml的配置文件与spring.factories下的配置类之间有什么联系？

在.properties/.yaml的配置文件中能配置的东西，都存在一个固定的规律，它们都是存在于xxxproperties类中的属性。

<font color='red'>具体的逻辑就是spring.factories下的xxxAutoConfiguration配置类与xxxproperties类是绑定的，然后xxxproperties又是与.properties/.yaml等配置文件进行绑定的！</font>我们就可以使用自定义配置了！

<font color='orange'> 一旦spring.factories中的某个配置类生效；这个配置类就会给容器中添加各种组件；这些组件的属性是从对应的xxxproperties类中获取的，这些类里面的每一个属性又是和.properties/.yaml配置文件绑定的；</font>

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222151305713.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




### 如何看哪些配置类生效了哪些没有生效？

在.properties/.yaml配置文件中加上debug=ture即可看到相关日志了：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222151334649.png#pic_center)




生效的：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021022215135077.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


等等等...



未生效的：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210222151409663.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


等等等...