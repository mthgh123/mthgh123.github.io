---
title: Value注解覆盖构造函数所设置的初值
date: 2021-03-15 20:23:15
categories:
- Spring学习
tags:
- Spring
---

![](http://qiniusave.codeyu.cn/Spring.jpg)

<!--less-->

---



<font color='red'>*在自己写demo的时候，发现@Value注解的赋值会覆盖构造函数的赋值。**</font>

对这句话的解释采用下述的案例进行解释：

分别定义一个配置类和一个pojo类：

```java
//配置类定义
@Configuration
public class AppConfig {

    @Bean
    public User getUser(){
        return new User("zozi");
    }
}

//pojo类定义;
public class User {
    @Value("chichi")
    private String name;

    public User(){}

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
```

上方的pojo类通过@Value注解的方式给name字段注入了初始值："chichi"，而配置类中有一个返回User类实例的bean方法，该方法的返回实例由构造函数return new User("zozi");构造之，该类实例中的name字段的初始值为"zozi"。

然后写一个测试类进行测试：

```java
public class MyTest {
    public static void main(String[] args) {
        //AnnotationConfigApplicationContext的构造方法有传入一个类作为参数的
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        User user = context.getBean("getUser", User.class);
        System.out.println(user.toString());
    }
}
```

这个测试样例获取的是配置类中的getUser()方法所构造的bean，但是该测试样例最终的返回值确是：User{name='chichi'}，<font color='red'>**这也就说明了@Value注解的值最终覆盖了构造函数的值！**</font>

注意：即使将@Value注解标注到set方法上也无法解决该问题！

```java
//pojo类定义;
public class User {
    private String name;

    public User(){}

    public User(String name){
        this.name=name;
    }

    public String getName() {
        return name;
    }

    @Value("chichi")
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
```

仍然会出现一样的问题！

