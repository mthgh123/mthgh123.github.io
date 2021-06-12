---
title: Spring学习笔记
date: 2021-03-17 18:30:06
categories:
- Spring学习
tags:
- Spring
---

![](http://qiniusave.codeyu.cn/Spring.jpg)

<!--less-->

---

## 1.Spring

### 1.1简介

spring的作用：

- <font color='red'>**目的：解决企业应用开发的复杂性**</font>
- 功能：使用基本的javabean代替EJB，并提供更多的企业应用功能
- <font color='red'>**范围：任何Java应用都可以应用spring**</font>
- 理念：使现有的技术更加容易使用，本身是一个大杂烩，整合了现有的技术框架！

Spring是一个轻量级控制反转（IOC）和面向切面编程（AOP）的容器框架！



spring的来源：

- 2002年，首次推出了Spring框架的雏形：interface21框架
- 2004年3月24号，以interface21框架为基础，经过重新设计，发布了Spring 1.0版
- Spring Framework创始者：Rod Johnson（音乐学博士）



Spring Framework官网：https://spring.io/projects/spring-framework#learn



导入的包：

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.4</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.4</version>
</dependency>
```



### 1.2优点（重点，面试常聊）

- Spring是一个开源免费的框架（容器）！
- Spring是一个轻量的，非入侵式的框架！
- <font color='red'>控制反转（IOC），面向切面编程（AOP）！</font>
- 支持事务的处理，对框架整合的支持！

<font color='blue'>总结一句话：Spring就是一个轻量级的控制反转（IOC）和面向切面编程（AOP）的框架！</font>



### 1.3组成

连接Spring的7大模块：https://www.cnblogs.com/xiaobaizhiqian/p/7616453.html

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210315155304507.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




### 1.4拓展

- SpringBoot
  - 一个快速开发的脚手架
  - 基于SpringBoot可以快速的开发单个微服务
  - 约定大于配置
- SpringCloud
  - SpringCloud是基于SpringBoot实现的

<font color='red'>因为现在大多数公司都在使用SpringBoot进行快速开发，学习SpringBoot的前提，需要完全掌握Spring及SpringMVC框架！</font>

<font color='blue'>弊端：发展了太久之后，违背了原来的理念！配置十分繁琐（在SpringBoot还未出来前）</font>



## 2.IOC理论推导 

### 2.1<font color='red'>没有使用注入思想的业务代码</font>

```java
//-------------dao层--------------
//UserDao接口
public interface UserDao {
    void getUser();
}
//它有两个实现类UserDaoImpl和UserDaoMysqlImpl
//UserDaoImpl
public class UserDaoImpl implements UserDao {
    @Override
    public void getUser() {
        System.out.println("获取默认用户数据！");
    }
}
//UserDaoMysqlImpl
public class UserDaoMysqlImpl implements UserDao {
    @Override
    public void getUser() {
        System.out.println("Mysql获取用户数据！");
    }
}
//-------------service层--------------
//UserService
public interface UserService {
    void getUser();
}
//UserServiceImpl
public class UserServiceImpl implements UserService {
	//此处实现类的注入
    private UserDao userDao = new UserDaoMysqlImpl();

    @Override
    public void getUser() {
        userDao.getUser();
    }
}

//-------------Test类--------------
public class MyTest {
    public static void main(String[] args) {
        //用户实际调用的是业务层
        UserService userService = new UserServiceImpl();
        userService.getUser();
    }
}
```

运行上述测试类之后，控制台返回的数据为：Mysql获取用户数据！

<font color='red'>因为在UserServiceImpl类中new的是一个 private UserDao userDao = new UserDaoMysqlImpl();类，所以调用的是该实现类中的getUser()方法；</font>

<font color='red'>**而如果我们想要调用UserDaoImpl实现类的getUser()方法，我们需要去修改UserServiceImpl的代码，即将 private UserDao userDao = new UserDaoMysqlImpl();改为：private UserDao userDao = new UserDaoImpl();**</font>

那么这就出现了一个问题：假设不同的实现类对应不同的需求，那当我们要改变需求的时候，每次都要去修改源代码。而当我们使用注入方式之后：

### 2.2<font color='red'>使用了注入思想的代码</font>

```java
//-------------dao层--------------
//UserDao接口
public interface UserDao {
    void getUser();
}
//它有两个实现类UserDaoImpl和UserDaoMysqlImpl
//UserDaoImpl
public class UserDaoImpl implements UserDao {
    @Override
    public void getUser() {
        System.out.println("获取默认用户数据！");
    }
}
//UserDaoMysqlImpl
public class UserDaoMysqlImpl implements UserDao {
    @Override
    public void getUser() {
        System.out.println("Mysql获取用户数据！");
    }
}
//-------------service层--------------
//UserService
public interface UserService {
    void getUser();
}
//UserServiceImpl
public class UserServiceImpl implements UserService {
	//此处实现类的注入
    private UserDao userDao;
    
    //利用set进行动态实现值的注入
    public void setUserDao(UserDao userDao){
        this.userDao=userDao;
    }

    @Override
    public void getUser() {
        userDao.getUser();
    }
}

//-------------Test类--------------
public class MyTest {
    public static void main(String[] args) {
        //用户实际调用的是业务层
        UserService userService = new UserServiceImpl();
        //下方的这一段代码即以set的方式实现了类的注入，那么我们不需要去修改之前实现类中的代码，即可根据不同的需求，在测试类中实现
        ((UserServiceImpl)userService).setUserDao(new UserDaoMysqlImpl());
        userService.getUser();
    }
}
```

### 2.3思想上的转变

上述使用的注入思想的代码实际上是一种思想上的转变！即使用一个set接口实现：

```java
//此处实现类的注入
private UserDao userDao;

//利用set进行动态实现值的注入
public void setUserDao(UserDao userDao){
    this.userDao=userDao;
}
```



#### 发生的变化：

- 之前，程序是主动创建对象！控制权在程序员手上！
- 使用了set注入后，程序不再具有主动性，而是变成了被动的接收对象！

**上面这个思想与IOC/DI的思想是一致性的：IOC/DI具体的讲：在传统的程序设计过程中，通常由<font color='red'>调用者</font>来创建<font color='red'>被调用者</font>的实例。但在spring中创建被调用者的工作不再由调用者来完成，因此称为控制反转。创建被调用者的工作由spring来完成，然后注入调用者，因此也称为依赖注入**

<font color='red'>**所谓控制反转就是：获得依赖对象的方式反转了！**</font>

<font color='blue'>控制反转是一种通过描述（xml或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IOC容器，其实现方法是依赖注入（DI）！</font>

<font color='red'>**IOC：控制反转；DI：依赖注入。实现IOC的方式有很多种，DI只是实现IOC的一种方式！**</font>

而通过依赖注入得实现IOC得方式有：<font color='red'>1.通过xml配置实现</font>、<font color='blue'>2.通过注解方式实现</font>

<font color='blue'>依赖注入（DI）的实现方式又有：1.通过set方法实现依赖注入，2.通过反射来实现依赖注入（@Autowired注解实现的依赖注入就是采用的反射方式）</font>

注意：采用xml方式配置Bean的时候，Bean的定义信息和实现是分离的，而采用注解的方式可以把两者结合为一体，Bean的定义信息直接以注解的形式定义在实现类中，从而达到了零配置的目的！

```java
//上述的调用者创建被调用者的实例，比如：没有使用注入思想的UserServiceImpl实现类中
//UserServiceImpl
public class UserServiceImpl implements UserService {
	//此处实现类的注入
    private UserDao userDao = new UserDaoMysqlImpl();

    @Override
    public void getUser() {
        userDao.getUser();
    }
}
//调用者是UserServiceImpl类，被调用者是UserDaoMysqlImpl类对象，调用者通过new来创建了一个被调用者UserDaoMysqlImpl实例。
//但在使用了set思想的UserServiceImpl实现类中：
//UserServiceImpl
public class UserServiceImpl implements UserService {
	//此处实现类的注入
    private UserDao userDao;
    
    //利用set进行动态实现值的注入
    public void setUserDao(UserDao userDao){
        this.userDao=userDao;
    }

    @Override
    public void getUser() {
        userDao.getUser();
    }
}
//调用者不再new一个UserDaoMysqlImpl实例/UserDaoImpl实例，即创建被调用者的工作不再由调用者来实现（控制反转IOC）。上方是通过set注入的思想，而spring注入思想与之类似，且更为巧妙，通过spring来完成这些实例对象的创建与管理工作！待调用者需要使用时，由spring注入（依赖注入DI）其中即可！
```

<font color='blue'>这种思想从本质上解决了问题，程序员不用再去管理对象的创建了！</font>系统的耦合性更低！



<font color='red'>spring容器在初始化时先读取配置文件，根据配置文件或元数据创建与组织对象存入容器中，程序使用时再从IOC容器中取出需要的对象：</font>

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210315155353344.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




## 3.HelloSpring

1.新建pojo类（entity类）

```java
package com.codeyu.pojo;

public class Hello {
    private String str;

    public String getStr() {
        return str;
    }

    public void setStr(String str) {
        this.str = str;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}
```

2.新建xml文件并配置相应的bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--使用Spring来创建对象，在Spring中这些都称为bean-->
    <bean id="hello" class="com.codeyu.pojo.Hello">
        <property name="str" value="spring hello world?"/>
    </bean>

</beans>
```

3.测试类中进行测试：

```java
import com.codeyu.pojo.Hello;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        //获取spring的上下文对象！这一行代码是通用操作！
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        //我们的对象现在都在spring中管理了。如果要使用，直接去里面取出来即可！
        Hello hello = (Hello) context.getBean("hello");
        System.out.println(hello.getStr());
    }
}

//返回结果为：spring hello world?
```

上方的代码中：

<font color='red'>Hello对象是由Spring创建的，hello对象的属性也是由Spring容器设置的！</font>

<font color='cornflowerblue'>控制</font>：谁来控制对象的创建，传统应用程序的对象由程序本身控制创建的，使用Spring后，对象是由Spring创建的。

<font color='cornflowerblue'>反转</font>：程序本身不创建对象，而编程被动接收对象！

<font color='cornflowerblue'>依赖注入</font>：就是利用set方法进行注入的。

<font color='red'>**可以通过ClassPathXmlApplicationContext的类去看一下底层的代码细节！**</font>



## 4.IOC创建对象的方式

<font color='red'>1.Spring默认的使用无参构造创建对象</font>

2.假设我们要使用有参构造创建对象

```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>
```

3.通过参数名

```xml
<bean id="user" class="com.codeyu.pojo.User">
    <constructor-arg name="name" value="chichi"/>
</bean>
```



### 总结：

<font color='red'>在配置文件加载之后，容器就会对相应的bean进行初始化，即创造出相应的实例对象！</font>

 

## 5.Spring配置

### 5.1 alias（别名）

\<alias\>



### 5.2 bean的配置

id：唯一标志符

class：bean对象对应的全限定类名

name：也相当于别名的作用



### 5.3 import

一般用于团队开发，可以将其他的.xml的bean配置文件导入一个.xml的bean配置文件！相当于将所有的.xml的bean配置文件合并为一个！

```xml
<import resource="beans.xml"/>
```



## 6.依赖注入

### 6.1构造器注入

前面已经讲过

### 6.2Set方法注入【重点】

- 依赖注入：Set注入！
  - 依赖：bean对象的创建依赖于容器！
  - 注入：bean对象中的所有属性，由容器来注入！



因为现在普遍使用注解方式进行配置，xml配置bean的方式逐渐少见，故此处不做笔记！



### 6.4 bean的作用域

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021031515540998.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


<font color='red'>重点掌握前两个作用域即可！</font>

默认实现是singleton模式（即单例模式），即全局共享一个实例对象！（<font color='red'>spring容器只会创建一个类的一个实例对象</font>）

#### 如何设置模式：

1.单例模式（singleton）

```xml
<bean id="user" class="com.codeyu.pojo.User" scope="singleton">
    <property name="name" value="chichi"/>
</bean>
<!==在scope字段后加对应的作用域==>
```

2.原型模式（prototype）：每次从容器中取出bean的时候，都是一个新的对象！

```xml
<bean id="user" class="com.codeyu.pojo.User" scope="prototype">
    <property name="name" value="chichi"/>
</bean>
```

3.其余的request、session、application这些只能在web开发中使用到！



## 7.Bean的自动装配

<font color='red'>上方所讲解的内容，关于bean的配置等都是属于我们手动配置的，需要我们为每一个bean去挨个配置！</font>

而关于bean的自动配置如下：

- 自动配置是Spring满足bean依赖的一种方式！
- Spring会在上下文自动寻找，并自动给bean装配属性！



<font color='blue'>在Spring中有三种装配的方式：</font>

1.在xml中显示的配置

2.在java中显示配置

3.隐士的自动装配



### 7.1测试

环境搭建：一个人，两个宠物！



### 7.2 ByName自动装配

```xml
<!--
autowire="byName" : 会自动在容器上下文中去查找，和自己对象set方法后面的值对应的bean id！
-->
<bean id="people" class="com.codeyu.pojo.People" autowire="byName">
    <property name="name" value="chichi"/>
</bean>
```

### 7.3 ByType自动装配

```xml
<!--
autowire="byName" : 会自动在容器上下文中去查找，和自己对象属性相同的bean类型！
-->
<bean id="people" class="com.codeyu.pojo.People" autowire="byName">
    <property name="name" value="chichi"/>
</bean>
```

<font color='blue'>但是ByType方式的自动装配需要全局只有一个该类的bean，否则会自动装配失败！</font>



### 小结：

- ByName的时候，需要保证所有bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值（即形参名）一致！
- ByType的时候，需要保证所有bean的class唯一，并且这个bean需要和自动注入的属性的类型一致！



### 7.4 使用注解实现自动装配

jdk1.5支持的注解！Spring从2.5开始支持的注解！

**要使用注解须知：**

1.导入约束：xmlns:context="http://www.springframework.org/schema/context"

2.配置注解的支持：\<context:annotation-config/\>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```



#### @AutoWired

<font color='red'>@AutoWired注解是spring的注解！</font>

<font color='red'>它的作用是将spring容器中的bean注入到被该注解标注了的字段中去！</font>使用的前提是spring容器中已经加入了与被注入字段类型相对应的bean！否则会报空指针异常！

<font color='red'>**使用@AutoWired注解来实现依赖注入时，类中的set方法可以不需要定义了，因为这是通过反射的方式进行依赖注入的。而不是通过set方式来实现依赖注入的！**</font>



  ```java
//在bean已经配置完毕的情况下，通过@Autowired注解实现依赖注入
//当@Autowired注解的required属性为false时，代表该注入值可以为null，即注入给Cat类的对象可以为null(即允许Cat类实例对象在容器中不存在)
@Autowired(required = false)
private Cat cat;
  ```



#### 与@Qualifier注解组合起来使用

当applicationContext.xml中有多个同类型的bean时：

```xml
<bean id="cat111" class="com.codeyu.pojo.Cat"/>
<bean id="dog222" class="com.codeyu.pojo.Dog"/>
<bean id="cat11" class="com.codeyu.pojo.Cat"/>
<bean id="dog22" class="com.codeyu.pojo.Dog"/>
```

此时的注入上述bean实例的地方会报错：

```java
@Autowired
private Cat cat;
@Autowired
private Dog dog;
```

<font color='red'>原因就是@Autowired注解不知道给被注解的字段注入哪一个实例，此时需要借助于@Qualifier注解来一起使用</font>，如下：

```java
@Autowired
@Qualifier(value = "cat11")
private Cat cat;
@Autowired
@Qualifier(value = "dog222")
private Dog dog;
```

此时则不会报错，因为可以实现正常的注入操作！

<font color='red'>如果@Autowired自动装配的环境比较复杂，自动装配无法通过一个注解【@Autowired】来完成的时候，我们可以使用@Qualifier(value="")注解来配合@Autowired一起使用，来唯一指定一个bean对象！</font>



#### @Resource

<font color='red'>@AutoWired注解是spring的注解！</font>

而与之相对应的是java的注解：@Resource，但是它实现的注入，在代码的左边没有叶子标志提示！

使用方式：

```java
@Resource
private Cat cat;
@Resource
private Dog dog;
```

@Resource注解首先会根据被标注字段的名字（如此处的：cat或dog）去spring容器中寻找id与cat或dog相对应的bean，如果没有的话，那么会去spring容器中寻找被标注字段的类型与Cat或Dog相对应的bean。

@Resource一个注解就可以实现@Autowired+@Qualifier注解的作用：

```java
@Resource(name="cat11")
private Cat cat;
@Resource(name="dog22")
private Dog dog;
```



#### 结论：

开发中最常用的注解还是@Autowired，但@Resource功能会更强大一点！

@Autowired和@Resource区别：

- 都是用来<font color='red'>自动装配的</font>，都可以放在属性字段上
- @Autowired默认通过byType的方式实现，而且必须要求这个对象存在！【常用】
- @Resource默认通过byName的方式实现，如果找不到名字，则通过byType实现！如果两个都找不到的情况下，就报错！
- 执行顺序不同：@Autowired通过byType的方式实现。@Resource默认通过byName的方式实现！

<font color='red'>**解释：什么是自动装配，就是当spring容器新建完相应的bean之后，需要将对应的bean注入到所需要的字段中去，注入的这个过程不需要程序员人为操作，而是通过事先配置或注解的方式由spring来自动来完成，所以称之为自动装配！**</font>





### 注解说明：

- @Autowired：自动装配先通过类型，再通过名字
  - 如果Autowired不能唯一自动装配上属性，则需要通过@Qualifier(value = "")
- @Resource：自动装配先通过名字，再通过类型
- @Nullable：字段标记了这个注解，说明这个字段可以为null
- Component：该注解放在类上，说明这个类被Spring管理了，就是bean



## 8.使用注解开发

在Spring4之后，要使用注解开发，必须要保证aop的包已被导入！

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210315155443151.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<font color='red'>**使用注解需要导入context约束，增加注解的支持！**</font>

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021031515545640.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">


    <context:annotation-config/>

</beans>
```

context约束还可以配置扫描具体的包：

```xml
<context:component-scan base-package="com.codeyu.pojo"/>
```

即：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.codeyu.pojo"/>
    <context:annotation-config/>

</beans>
```



#### 重点

<font color='red'>在类的上面的这些注解：@Component、@Service、@Controller、@Repository：</font>

```java
@Component
public class User {
    //类定义
}

@Service
public class UserService {
    //类定义
}
```

<font color='red'>等价于在applicationContext.xml中配置bean：</font>

```xml
<bean id="dog22" class="com.codeyu.pojo.Dog"/>
```

<font color='red'>但是你会发现标志了@Component/@Service类它们没有id，实际上，它们的id就是类名，但第一个字母小写！</font>比如：UserService就是userService

<font color='red'>然后，之前给bean中的字段注入初始值的时候，需要这样注入：</font>

```xml
<!--或者这样注入：给User类型的bean中的String类型的name字段注入初始值"chichi"-->
<bean id="user" class="com.codeyu.pojo.User" scope="prototype">
    <property name="name" value="chichi"/>
</bean>
```

<font color='red'>现在注入初始值只需要在字段上写上@Value注解即可！</font>

下方的这个值的注入：

```xml
<bean id="user" class="com.codeyu.pojo.User" scope="prototype">
    <property name="name" value="chichi"/>
</bean>
```

等价于：

```java
@Component
public class User {
    @Value("chichi")
    private String name;
}
```

但是更复杂的注入的话还是使用xml形式的要更方便一些！@Value注解还可以被放在set方法上，给形参赋值，如：

```java
@Value("chichi")
public void setName(String name){
    this.name=name;
}
```



### 1.bean

#### @Bean注解

<font color='red'>bean注解是一个只能标注在方法上的注解，用于将该方法所返回的对象实例注册为bean实例。默认的该bean实例的id为被注解的方法名，当然也可以通过@Bean注解的name属性来指定bean的名字！</font>见博客：https://blog.csdn.net/yuyeqianhen/article/details/90177597

<font color='blue'>**@Bean是一个方法级别上的注解，主要用在@Configuration注解的类里，也可以用在@Component注解的类里。作用为注册bean对象。**</font>

所以两种方式指定@Bean注解所标注的bean的名字：

方式1：通过@Bean注解的name属性指定。此时在以byName的方式获取bean的时候只能用userbean1，而如果此时用了方法名：getUser来获取bean的话就会报错！

```java
@Service
public class UserService {
    @Bean(name = "userbean1")
    public User getUser(){
        User user = new User("chichi", "男");
        System.out.println("chichi已被装入bean");
        return user;
    }
}
```

方式2：通过<font color='blue'>方法名</font>以byName的方式来获取bean。此处方法名为：getUser，但此时@Bean注解后不可有name属性

```java
@Service
public class UserService {
    @Bean
    public User getUser(){
        User user = new User("chichi", "男");
        System.out.println("chichi已被装入bean");
        return user;
    }
}
```

到目前位置，获取bean仍然离不开applicationContext.xml配置文件以及在取bean的时候需要通过下方的方法来进行：ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");然后再由context来getBean

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        /*User user1 = context.getBean("userbean1", User.class);
        System.out.println(user1.toString());*/
        User user2 = context.getBean("user", User.class);
        System.out.println(user2.toString());
        User user3 = context.getBean("getUser", User.class);
        System.out.println(user3.toString());
    }
}
```



### 2.属性如何注入

<font color='red'>然后，之前给bean中的字段（属性）注入初始值的时候，需要这样注入：</font>

```xml
<!--或者这样注入：给User类型的bean中的String类型的name字段注入初始值"chichi"-->
<bean id="user" class="com.codeyu.pojo.User" scope="prototype">
    <property name="name" value="chichi"/>
</bean>
```

<font color='red'>现在注入初始值只需要在字段上写上@Value注解即可！</font>

下方的这个值的注入：

```xml
<bean id="user" class="com.codeyu.pojo.User" scope="prototype">
    <property name="name" value="chichi"/>
</bean>
```

等价于：

```java
@Component
public class User {
    @Value("chichi")
    private String name;
}
```

但是更复杂的注入的话还是使用xml形式的要更方便一些！@Value注解还可以被放在set方法上，给形参赋值，如：

```java
@Value("chichi")
public void setName(String name){
    this.name=name;
}
```



### 3.衍生的注解

@Component的衍生注解：@Service、@Controller、@Repository。

- dao层：@Repository
- service层：@Service
- controller层：@Controller

这四个注解功能是一样的，都是代表将某个类注册到Spring中，装配bean！



### 4.自动配置

即：

- @Autowired：自动装配先通过类型，再通过名字
  - 如果Autowired不能唯一自动装配上属性，则需要通过@Qualifier(value = "")
- @Resource：自动装配先通过名字，再通过类型



### 5.作用域

@Scope注解：这个注解可以标注在类上或方法上！

- 单例模式：@Scope("singleton")
- 原型模式：@Scope("prototype")

### 6.小结

xml与注解：

- xml更加万能，适用于任何场合！维护简单方便
- 注解：不是自己类使用不了，维护相对复杂

xml与注解最佳实践：

- xml来管理bean

- 注解只负责完成属性的注入

- 在使用过程中，必须让注解生效，就是开启注解的支持：

  ```xml
  <!--指定扫描的包，这个包下被注解标注为bean的类就会被加入到spring容器中！-->
  <context:component-scan base-package="com.codeyu.pojo,com.codeyu.service"/>
  <context:annotation-config/>
  ```





## 9.使用java的方式配置Spring

使用java类的配置完全取代applicationContext.xml文件式的配置。

类似于：

```java
@Configuration
public class AppConfig {

    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
```

它完全可以取代：

```xml
<beans>
    <bean id="myService" class="com.acme.services.MyServiceImpl"/>
</beans>
```

-------------------

在java类上加上@Configuration注解之后类似于applicationContext.xml文件中写上\<beans\>标签！

<font color='red'>接下来完全不使用applicationContext.xml文件式的配置方式，全权交给java来做！</font>

JavaConfig是Spring的一个子项目，在Spring4之后，称为核心！



### 关于@Bean以及@Configuration注解：

1.@Bean是一个方法级别上的注解，主要用在@Configuration注解的类里，也可以用在@Component注解的类里。作用为注册bean对象。

2.@Bean注解在返回实例的方法上，如果未通过@Bean指定bean的名称，则默认与标注的方法名相同；

3.@Bean注解默认作用域为单例singleton作用域，可通过@Scope(“prototype”)设置为原型作用域；

4.既然@Bean的作用是注册bean对象，那么完全可以使用@Component、@Controller、@Service、@Ripository等注解注册bean，当然需要配置@ComponentScan注解进行自动扫描。



**实现了ApplicationContext接口的类有:**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210315155547471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


之前使用applicationContext.xml文件时，我们使用的是ClassPathXmlApplicationContext()类，现在使用注解配置bean后，则使用上述用红框框起来的两个类！



### 如何在一个配置类中引入另一个配置类

之前在.xml文件中配置bean时，.xml文件可以有多个，最终通过import标签汇聚成一个。

<font color='red'>对于@Configuration标注的配置类也可以有多个，之后通过@Import注解将其他的配置类导入一个配置类即可！</font>

如下所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210315155603874.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


```java
@Configuration
@ComponentScan("com.codeyu.pojo")
@Import(AppConfig2.class) //通过@Import注解导入
public class AppConfig {
    //配置体
}
```



### 配置类中bean不需要@ComponentScan

<font color='red'>**对于类上标注了@Configuration的注解类，该类中的标注了@Bean注解的方法所返回的实例对象可以注册到spring中作为bean，而不需要加入到@ComponentScan中进行扫描。而其他的@Service、@Controller、@Repository、@Component标注的类必须被@ComponentScan扫描到才行！**</font>（实际上，@Service、@Controller、@Repository、@Component类中也可以写@Bean方法，但是这些类也必须事先被@ComponentScan扫描才行，否则不会像@Configuration的配置类中的@Bean方法一样被注册到spring中）



### 测试样例：

新建一个配置类和一个实体类以及一个测试类：

```java
//配置类
@Configuration
@ComponentScan("com.codeyu.pojo")
public class AppConfig {

    //注册一个bean，就相当于我们之前在.xml文件中写的一个bean标签
    //这个方法的名字，就相当于bean标签中的id属性
    //这个方法的返回值，就相当于bean标签终得class属性
    @Bean
    public User getUser(){
        System.out.println("构造了zozi");
        return new User("zozi"); //该return 就是返回要注入到bean中的对象！
    }
}

//注意，上方的@Configuration注解只会让@Configuration注解的类以及其中的@Bean注解标注的类被加入到spring容器中去，不会将其他的被@Service、@Controller、@Repository、@Component标注的类加入到spring容器中去。要想将@Service、@Controller、@Repository、@Component标注的类加入到spring容器中去，需要加@ComponentScan("com.codeyu.pojo")

//-------------------------------------
//pojo类

@Component
@Scope("singleton")
public class User {
    @Value("chichi")
    private String name;

    public User(){
        System.out.println("无参构造被调用了！");
    }

    public User(String name){
        this.name=name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}

//---------------------------------
//测试类
public class MyTest {
    public static void main(String[] args) {
        //AnnotationConfigApplicationContext的构造方法有传入一个类作为参数的
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        User user1 = context.getBean("getUser", User.class);
        System.out.println("user1:"+user1.toString());
        User user2 = context.getBean("user", User.class);
        System.out.println("user2:"+user2.toString());
        System.out.println(user1==user2);
    }
}

//测试返回结果为：
/*
无参构造被调用了！
构造了zozi
user1:User{name='chichi'}
user2:User{name='chichi'}
false
*/
```

出现上述结果的解释：

无参构造被调用了！：说明调用了pojo类User的无参构造！即User类对象被无参构造构造出来，然后给name字段赋值"chichi"，并加入到spring容器中去了

构造了zozi：说明配置类中的下方这个方法返回的bean被加入到了spring容器中：

```java
@Bean
public User getUser(){
    System.out.println("构造了zozi");
    return new User("zozi"); //该return 就是返回要注入到bean中的对象！
}
```

user1:User{name='chichi'}
user2:User{name='chichi'}：这个说明实例对象的值都被@Value注解的值给重新赋值了，见另一篇博客的解释！

false：说明即使给User类上标注了作用域为singleton，但spring容器中仍然有多余一个的User实例对象