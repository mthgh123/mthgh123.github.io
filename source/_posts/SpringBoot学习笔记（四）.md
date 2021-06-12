---
title: SpringBoot学习笔记（四）
date: 2021-02-15 20:09:40
categories:
- SpringBoot学习
tags:
- SpringBoot
---

![](http://qiniusave.codeyu.cn/springboot.jpg)

<!--less-->

---


## Swagger

学习目标：

- 了解Swagger的作用和概念
- 了解前后端分离
- 在SpringBoot中集成Swagger



### Swagger简介

**前后端分离**

Vue + SpringBoot

**后端时代**：前端只用管理静态页面；前端只用写一个html页面，然后将其给后端。后端再通过模板引擎，比如 JSP来处理===>那时后端是主力



**前后端分离时代**

- 后端：后端控制层，服务层，数据访问层【后端团队】

- 前端：前端控制层，视图层【前端团队】
  - 伪造后端数据，json，也就是在后端开发之前，前端依旧能够跑起来，因为其有伪造的数据！但最后前后端联调的时候则是通过后端获取的数据了

- 前后端如何交互？ ===> API 接口
- 前后端相对独立，松耦合；
- 前后端甚至可以部署到不同的服务器上；



产生一个问题：

- 前后端集成联调，前端人员和后端人员无法做到，及时协商，尽早解决，最终导致问题集中爆发；

决绝方案：

- 首先指定一个schema，实时更新最新的API，降低集成的风险；
- 早些年：制定word计划文档；
- 前后端分离：
  - 前端测试后端工具：postman 【不太好用】
  - 后端提供接口，需要实时更新最新的消息及改动！



<font color='orange'>所以Swagger应运而生！</font>



### Swagger

- 号称世界上最流行的Api框架；
- RestFul Api 文档在线生成工具=><font color='blue'>Api文档与API定义同步更新</font>
- 可以直接运行，可以在线测试API接口
- 支持多种预压：Java、Php...



官网：https://swagger.io/



### 在项目中使用Swagger

在项目中使用Swagger需要Springfox，引入这个jar包，该jar包中包括：

- swagger2
- ui

两个组件！



### SpringBoot集成Swagger

1.新建一个SpringBoot Web项目

2.在pom.xml中加入相关依赖项

```xml
<!-- springfox-swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>

<!-- springfox-swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>

<!-- 注意：3.0.0 版本的需要引入springfox-boot-starter即可，但是2.9.2版本的则是引入上述的两个依赖 -->
```

3.编写一个hello world!工程，检测项目启动是否正常

4.配置Swagger===><font color='red'>写一个Config配置类</font>

```java
@Configuration
@EnableSwagger2 //开启Swagger2 ：Swagger2 与 Swagger 不一样，Swagger是老版的，Swagger2是新版的
public class SwaggerConfig {
    //编写配置代码
}
```

5.测试运行

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301162047161.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




### 配置Swagger

Swagger的bean实例Docket：

在Config包下新建SwaggerConfig配置类：

```java
package com.example.swaggerdemo.config;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import java.util.ArrayList;

@Configuration
@EnableSwagger2 //开启Swagger2 ：Swagger2 与 Swagger 不一样，Swagger是老版的，Swagger2是新版的
public class SwaggerConfig {


    //配置了Swagger的Docket的bean实例
    @Bean
    public Docket docket(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                //RequestHandlerSelectors，配置要扫描接口的方式
                //basePackage：指定要扫描的包
                //如果此处要定义扫描全部的话，则是RequestHandlerSelectors.any()
                // 如果此处要定义都不扫描话，则是RequestHandlerSelectors.none()
                //指定要扫描的包 RequestHandlerSelectors.basePackage("com.example.swaggerdemo.controller")
                //扫描类上的注解 RequestHandlerSelectors.withClassAnnotation(GetMapping.class)
                //扫描方法上的注解 RequestHandlerSelectors.withMethodAnnotation(GetMapping.class)
                .apis(RequestHandlerSelectors.basePackage("com.example.swaggerdemo.controller"))//一般多用RequestHandlerSelectors.basePackage()方法

                //paths() 过滤什么路径
                .paths(PathSelectors.ant("/kaung/**")) //只扫描带有/kaung这个请求路径下的接口

                .build();
    }


    //配置Swagger信息 = apiInfo
    private ApiInfo apiInfo(){

        //联系人信息
        Contact contact = new Contact("junjun", "http://www.codeyu.cn/", "5717xxx@qq.com");

        return new ApiInfo("吃吃的SwaggerAPI文档",
                "即使再小的帆也能远航",
                "1.0",
                "http://www.codeyu.cn/",
                contact,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList());
    }
}
```



### Swagger配置扫描接口

Docket.select()方法：

```java
//配置了Swagger的Docket的bean实例
    @Bean
    public Docket docket(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                //RequestHandlerSelectors，配置要扫描接口的方式
                //basePackage：指定要扫描的包
                //如果此处要定义扫描全部的话，则是RequestHandlerSelectors.any()
                // 如果此处要定义都不扫描话，则是RequestHandlerSelectors.none()
                //指定要扫描的包 RequestHandlerSelectors.basePackage("com.example.swaggerdemo.controller")
                //扫描类上的注解 RequestHandlerSelectors.withClassAnnotation(GetMapping.class)
                //扫描方法上的注解 RequestHandlerSelectors.withMethodAnnotation(GetMapping.class)
                .apis(RequestHandlerSelectors.basePackage("com.example.swaggerdemo.controller"))//一般多用RequestHandlerSelectors.basePackage()方法

                //paths() 过滤什么路径
                .paths(PathSelectors.ant("/kaung/**")) //只扫描带有/kaung这个请求路径下的接口

                .build();
    }
```



### 配置是否启动Swagger

```java
	//配置了Swagger的Docket的bean实例
    @Bean
    public Docket docket(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .enable(false) //是否启动Swagger，为false则不启动
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.example.swaggerdemo.controller"))//一般多用RequestHandlerSelectors.basePackage()方法
                .build();
    }
```



### 希望Swagger在生产环境使用，在发布的时候不使用？

- 判断是不是生产环境 flag=false
- 注入enable(flag)

```java
//配置了Swagger的Docket的bean实例
    @Bean
    public Docket docket(Environment environment){

        //设置要显示的Swagger环境
        Profiles profiles = Profiles.of("dev","test");

        //获取项目的环境:通过 environment.acceptsProfiles()判断是否处在自己设定的环境当中
        boolean flag = environment.acceptsProfiles(profiles);

        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .enable(flag) //是否启动Swagger，为false则不启动    此处监听flag，来确定生产/测试环境启用Swagger
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.example.swaggerdemo.controller"))//一般多用RequestHandlerSelectors.basePackage()方法
                .build();
    }
```



### 配置API文档的分组

配置分组的方法：

```java
.groupName("吃吃")
```



**如何配置多个分组？**

配置多个Docket即可！

```java
	@Bean
    public Docket docket1(){
        return new Docket(DocumentationType.SWAGGER_2).groupName("迟迟");
    }

    @Bean
    public Docket docket2(){
        return new Docket(DocumentationType.SWAGGER_2).groupName("次次");
    }
```



### 实体类配置

@ApiModel注解：对字段进行解释性描述

```java
@ApiModel("用户实体类")
public class User {

    @ApiModelProperty("用户名")
    public String userName;
    @ApiModelProperty("密码")
    public String password;
}
```



### 总结

1.我们可以通过Swagger给一些比较难理解的属性或者接口增加注释信息！

2.接口文档实时更新！

3.可以在线测试！

Swagger是一个优秀的工具，几乎所有大公司都有使用它！

【注意点】在正式发布的时候，关闭Swagger！！！出于安全考虑，而且节省运行内存！



##  任务

异步任务~

定时任务~

邮件发送~



### 异步任务

情景描述：假设点击返送邮件，点击之后，页面直接显示”邮件已发送“，而实际上邮件还在慢慢的发送过程中，这主要是为了增加用户体验，即点击按钮后，先由一个线程执行处理在页面上显示相应的信息，之后由另一个线程发送邮件。即异步任务！

但是问题时，写异步代码比较麻烦，尤其是场景比较多的时候！

好在springboot能帮助我们简化异步代码开发：

<font color='red'>**让springboot开启异步执行分以下几步：**</font>

1.在方法上加上@Async注解：那么项目在运行启动时就会帮我们开启一个线程池！

2.开启异步注解功能：在启动类上加上@EnableAsync注解！

看下方代码，在未开springboot异步处理之前：

```java
@Service
public class AsyncService {

    public void hello(){
        try {
            Thread.sleep(3000);
        } catch (Exception e) {
            e.printStackTrace();
        }

        System.out.println("数据正在处理");
    }
}

@RestController
public class AsyncController {

    @Autowired
    private AsyncService asyncService;

    @RequestMapping("/hello")
    public String hello(){
        asyncService.hello();
        return "3s latter display hello!";
    }
}

//项目启动后在浏览器输入：http://localhost:8080/hello
//等待3s后才会显示：3s latter display hello!
```

开启springboot异步处理之后：

```java
@Service
public class AsyncService {

    //告诉Spring这是一个异步的方法
    @Async
    public void hello(){
        try {
            Thread.sleep(3000);
        } catch (Exception e) {
            e.printStackTrace();
        }

        System.out.println("数据正在处理");
    }
}

@RestController
public class AsyncController {

    @Autowired
    private AsyncService asyncService;

    @RequestMapping("/hello")
    public String hello(){
        asyncService.hello();
        return "3s latter display hello!";
    }
}

//项目启动后在浏览器输入：http://localhost:8080/hello
//无需等待页面直接显示：3s latter display hello!
这是因为异步方法会创建两个线程，一个线程执行asyncService.hello();方法，另一个线程执行return "3s latter display hello!";方法
在浏览器输入：http://localhost:8080/hello 三秒后会马上显示3s latter display hello!，但是在控制台真正的3s后才会显示“数据正在处理”
```



### 邮件发送

在springboot项目中使用邮件发送：

分以下几步：

1.导入依赖包：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-main</artifactId>
</dependency>
```

2.在yaml/properties中配置相应属性

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301162123793.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


3.编写邮件发送的方法

```java
package com.example.springboot09task;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSenderImpl;
import org.springframework.mail.javamail.MimeMessageHelper;

import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;
import java.io.File;

@SpringBootTest
class Springboot09TaskApplicationTests {

    @Autowired
    JavaMailSenderImpl mailSender;

    @Test
    void contextLoads() {

        //一个简单的邮件
        SimpleMailMessage mailMessage = new SimpleMailMessage();

        mailMessage.setSubject("吃吃你好呀！"); //设置邮件主题
        mailMessage.setText("你超能吃的！"); //设置邮件正文内容
        mailMessage.setTo("2365941823@qq.com"); //设置邮件发送给谁
        mailMessage.setFrom("2365941823@qq.com"); //设置邮件是谁发送的

        mailSender.send(mailMessage);
    }

    @Test
    void test() throws MessagingException {
        //发送一个复杂的邮件
        MimeMessage mimeMessage = mailSender.createMimeMessage();
        //组装
        MimeMessageHelper helper = new MimeMessageHelper(mimeMessage,true);

        helper.setSubject("吃吃你好呀！");
        helper.setText("<p style='color:red'>你超能吃的！</p>",true);

        //附件
        helper.addAttachment("可爱猫头鹰.jpg",new File("D:\\需要进行备份的文件\\我的电子相片\\可爱猫头鹰.jpg"));

        helper.setTo("2365941823@qq.com");
        helper.setFrom("2365941823@qq.com");


        mailSender.send(mimeMessage);
    }


    //封装成方法

    /**
     *
     * @param html
     * @param subject
     * @param text
     * @throws MessagingException
     * @Author codeyu
     */
    public void sendMail(Boolean html, String subject, String text) throws MessagingException {
        //发送一个复杂的邮件
        MimeMessage mimeMessage = mailSender.createMimeMessage();
        //组装
        MimeMessageHelper helper = new MimeMessageHelper(mimeMessage,true);

        helper.setSubject(subject);
        helper.setText(text,html);

        //附件
        helper.addAttachment("可爱猫头鹰.jpg",new File("D:\\需要进行备份的文件\\我的电子相片\\可爱猫头鹰.jpg"));

        helper.setTo("2365941823@qq.com");
        helper.setFrom("2365941823@qq.com");

        mailSender.send(mimeMessage);
    }

}

```



### 定时任务

会使用到的两个接口：

```
TaskScheduler 任务调度器
和
TaskExecutor 任务执行器
```

开启定时任务，需要在springboot启动类上面注释：<font color='red'>@EnableScheduling</font> //开启定时任务

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210301162152973.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


使用定时功能的注解：<font color='red'>@Scheduled</font> //什么时候执行

需要掌握Cron表达式即可：

```java
package com.example.springboot09task.service;

import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Service;

@Service
public class ScheduledService {

    //在一个特定的时间执行这行方法
    //秒 分 时 日 月 周几~
    @Scheduled(cron = "20 42 15 * * 0-7") //含义：每一天的15时42分20秒    就会去执行下方的这个方法
    public void hello(){
        System.out.println("hello,你被执行了");
    }
}
```





## 分布式Dubbo+Zookeeper+SpringBoot

参考：https://mp.weixin.qq.com/s/sKu9-vH7NEpUd8tbxLRLVQ



### RPC

通信有两种方式，一种叫做http，一种叫做RPC

RPC两个核心模块：通讯、序列化（数据传输需要转换）

<font color='red'>RPC：Dubbo+Zookeeper</font>

<font color='red'>HTTP：Spring Cloud</font>



<font color='blue'>Dubbo底层的是Netty！后续有时间也可以学学这个！</font>



Dubbo解决的问题：通信+序列化

<font color='cornflowerblue'>Dubbo提供了六大核心能力：面向接口代理的高性能RPC调用，智能容错和负载均衡，服务自动注册和发现，高度可扩展能力，运行期流量调度，可视化的服务治理与运维。</font>



### Dubbo

官网：https://dubbo.apache.org/zh/

dubbo-admin是一个监控管理后台（即就是相当于一个后端管理系统的网站），它可以用来查看我们注册了哪些服务，哪些服务被消费了！

dubbo：是一个jar包



### Zookeeper

官网：https://zookeeper.apache.org/

zookeeper：一个注册中心



### 步骤

前提：zookeeper服务已开启！

1.提供者提供服务

		1.导入依赖
	
		2.配置注册中心的地址，以及服务发现名，和要扫描的包~
	
		3.在想要被注册的服务上面~增加一个注解@Service

2.消费者如何消费

		1.导入依赖
	
		2.配置注册中心的地址，配置自己的服务名
	
		3.从远程注入服务~（<font color='red'>需要将提供者和消费者放在同一个包下</font>）