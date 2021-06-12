---
title: JDBC学习笔记
date: 2021-03-03 22:03:24
categories:
- JDBC
tags:
- JDBC
---

![](http://qiniusave.codeyu.cn/jdbc.jpg)

<!--less-->

---

## JDBC（重点）

### 1.数据库驱动

驱动：声卡驱动、显卡驱动、所以数据库也要驱动

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306183336576.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


我们的程序会通过数据库驱动，和数据库打交道！（但是假设有10中数据库，难道也要相应的写10种数据库驱动吗？所以这个时候，JDBC出现了！）

### 2.JDBC

SUN公司为了简化开发人员（对数据库的统一）操作，提供了一个（Java操作数据库的）规范，俗称JDBC，这些规范的实现由具体的厂商去做！（即MySQL数据库的具体实现由MySQL厂商来做，Oracle的由Oracle来做）

那么上图变成了：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306183351493.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


编写JDBC代码需要两个包：

java.sql

javax.sql

还需要导入一个数据库驱动包，如：mysql-connector-java-8.0.13.jar



### 3.第一个JDBC程序

1.创建一个普通项目

2.导入数据库驱动对应的jar包：具体操作为在项目中新建一个`lib`目录，然后将jar包复制粘贴到该目录下，然后将该jar包添加为库。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306183406824.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


3.编写测试代码

```java
package com.example.lesson01;

import java.sql.*;

// 我的第一个jdbc数据库
public class JdbcFirstDemo {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        //1.加载驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        //2.用户信息和url
        String url = "jdbc:mysql://localhost:3306/csdn_spring_boot?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=GMT ";
        String username="root";
        String password="root";

        //3.连接成功，数据库对象
        Connection connection = DriverManager.getConnection(url, username, password);

        //4.执行SQL对象，执行SQL的对象
        Statement statement = connection.createStatement();

        //5.执行SQL的对象
        String sql = "select * from tb_user";

        ResultSet resultSet = statement.executeQuery(sql); //返回的结果集中封装了我们全部的查询出来的结果

        while (resultSet.next()){
            System.out.println("id="+resultSet.getObject("id"));
            System.out.println("name="+resultSet.getObject("name"));
            System.out.println("password="+resultSet.getObject("password"));
        }

        //6.释放连接
        resultSet.close();
        statement.close();
 
        connection.close();
    }
}
```

#### 步骤总结：

1.加载驱动

2.连接数据库DriverManager

3.获得执行sql的对象 Statement

4.获得返回的结果集

5.释放连接



#### URL:

```java
String url = "jdbc:mysql://localhost:3306/csdn_spring_boot?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=GMT";
```



#### Statement：执行SQL的类

PrepareStatement也是执行SQL的类，但此处我们只使用Statement类



#### ReslutSet：查询的结果集；封装了所有的查询结果





### 4.statement对象

jdbc中的statement对象用于向数据库发送SQL语句，想完成对数据库的增删改查，只需要通过这个对象向数据库发送增删改查语句即可。

Statement对象的executeUpdate方法，用于向数据库发送增、删、改的sql语句，executeUpdate执行完成，将会返回一个整数（即增删改查语句导致了数据库几行数据发生了变化）。

Statement.executeQuery方法用于向数据库发送查询语句，executeQuery方法返回代表查询结果的ResultSet对象。

#### CURD操作-create

使用executeUpdate(String sql)方法插入数据：

```java
Statement st = conn.createStatement();
String sql = "insert into user(...) values(...)";
int num = st.executeUpdate(sql);
if(num > 0) {
    System.out.println("插入成功！");
}
```



#### 如何获取到.properties中的数据用于数据库连接(注意：.properties配置文件是在src目录下新建的，在其他地方建容易扫描不到)

首先需要知道的是，为什么要将数据库连接时需要使用到的driver、url、username、password等数据放在.properties文件中，这主要是<font color=red>实现解耦，将配置属性与代码分离！</font>

获取.properties文件中属性数据的代码如下：

```java
package com.example.lesson01.utils;

import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

public class JdbcUtils {

    private static String driver = null;
    private static String url = null;
    private static String username = null;
    private static String password = null;

    //因为db.properties文件是在src目录下的，而src目录下的内容都可以通过缓存拿到
    static {
        try {
            //去拿到src路径下的db.properties文件资源
            InputStream inputStream = JdbcUtils.class.getClassLoader().getResourceAsStream("db.properties");
            Properties properties = new Properties();
            properties.load(inputStream);

            driver = properties.getProperty("driver");
            url = properties.getProperty("url");
            username = properties.getProperty("username");
            password = properties.getProperty("password");

            // 1.驱动只用加载一次
            Class.forName(driver);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    //获取连接
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url,username,password);
    }

    //释放连接资源
    public static void release(Connection conn, Statement st, ResultSet rs){
        if(rs!=null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if(st!=null){
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if(conn!=null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 5.SQL注入的问题

什么叫SQL注入，<font color='red'>即：sql存在漏洞，会被攻击导致数据泄露！</font>

sql注入即是web应用程序对用户输入数据的合法性没有判断或过滤不严！

sql注入一般常常采取的方式是在注入sql语句是使用"or"



### 6.PreparedStatement对象

PreparedStatement<font color='red'>可以防止sql注入</font>，并且效率更高！

<font color='cornflowerblue'>PreparedStatement防止sql注入的本质，是将传递进来的参数当作字符串！假设其中存在转义字符就直接转义，比如 ' 号会被直接转义！</font>

```java
package com.example.lesson01;

import com.example.lesson01.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class PreparedStTest {
    public static void main(String[] args) {
        Connection connection = null;
        PreparedStatement st = null;

        try {
            connection = JdbcUtils.getConnection();

            //使用? 占位符替代参数
            String sql = "insert into tb_user(id,`name`,`password`) values(?,?,?)";
            st = connection.prepareStatement(sql); //预编译SQL，先写sql，然后不执行

            //手动给参数赋值
            st.setInt(1,5);
            st.setString(2,"chichi");
            st.setString(3,"2333");

            //执行
            int i = st.executeUpdate();
            if(i>0){
                System.out.println("插入成功！");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            JdbcUtils.release(connection,st,null);
        }
    }
}
```

### 7.事务

<font color='red'>要么都成功，要么都失败 </font>

#### ACID属性

原子性：要么全部完成，要么都不完成

一致性：结果总数不变

<font color='blue'>隔离性：多个进程互不干扰</font>

持久性：一旦提交不可逆，持久化到数据库了



#### 隔离性的问题：

1.脏读：一个事务读取了另一个没有提交的事务

2.不可重读读：在同一个事务内，重复读取表中的数据，表数据发生了改变

3.虚读（幻读）：在一个事务内，读取到了别人插入的数据，导致前后读出来结果不一致



#### 代码实现

事务的处理流程分为下面几步：

1.开启事务`conn.setAutoCommit(false);`

2.一组业务执行完毕，提交事务

3.可以在catch语句中显示的定义回滚语句（当然了，即使不显示定义，当事务中途代码出错时，也会默认回滚，即别人的代码已经帮我们考虑了）

```java
package com.example.lesson01;

import com.example.lesson01.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

//模拟事务失败！
public class TestTransaction2 {
    public static void main(String[] args) throws SQLException {
        Connection conn = null;
        PreparedStatement st =null;
        ResultSet rs=null;

        try{
            conn= JdbcUtils.getConnection();
            // 关闭数据库的自动提交，自动会开启事务
            conn.setAutoCommit(false); //有些地方就会把这句代码描述为开启事务

            String sql1="update account set money = money-100 where name = 'A'";
            st = conn.prepareStatement(sql1);
            st.executeUpdate();

            //int x = 1/0; //报错，看事务是否回滚

            String sql2="update account set money = money+100 where name = 'B'";
            st = conn.prepareStatement(sql2);
            st.executeUpdate();

            //业务完毕，提交事务
            conn.commit();
            System.out.println("事务执行成功！");
        } catch (SQLException e){
            // 即使下方这个回滚的try-catch语句块不写，当事务中途报错时也会回滚，因为是默认回滚！
            /*try {
                conn.rollback(); //如果失败则回滚事务！即数据库执行的修改操作都回到最初的状态！
            } catch (SQLException ex) {
                ex.printStackTrace();
            }*/
            e.printStackTrace();
        } finally {
            JdbcUtils.release(conn,st,rs);
        }
    }
}
```

### 重点：数据库连接池

**数据库的使用过程：**数据库的连接--->执行--->执行完毕并释放

而在这个过程中，数据库的连接和释放是十分浪费资源的，开销非常大！所以就研究出了池化技术！

<font color='red'>池化技术：预先准备好可能需要使用的资源，等需要连接时则直接连接预先准备好的！</font>

如下述例子所示：

```java
//1.预先通过下述代码准备好几个连接了数据库的Connection对象，并放到一个池子里
Connection conn= JdbcUtils.getConnection();

//2.当需要执行sql语句时，从池子里拿一个Connection对象出来使用

//3.当使用完毕之后，也不要释放连接，而是又放回池子里，等待下一次使用
```

一般池子里放多少个Connection对象称之为连接数。

<font color='blue'>连接数又分为常用连接数，最小连接数，最大连接数</font>

**一般这么设定连接数：**

最小连接数：10

最大连接数：20

等待超时：100ms

当常用连接数为10个时，那么最小连接数也设置为10个，最大连接数则设置成业务最高承载上限！

如果当前设置的最大连接数为20个，而有30个在尝试连接，那么只能获得20个，剩下的10个尝试连接的则处于等待状态！

然后又有了等待超时的概念，即等待时间超过某个值之后认定其为等待超时！



#### 编写连接池，只需要实现DataSource接口

**目前市面上比较知名的实现类，即开源数据源实现有：**

DBCP

C3P0

Druid：阿里巴巴的

<font color='red'>使用了这些数据库连接池之后，我们在项目开发中就不需要编写连接数据库的代码了！</font>帮我们解决的问题就是：帮助我们连接数据库了，而不用我们自己一步一步配置来连接数据库！用代码表示就相当于：

```java
//帮我们解决了这一步：
Connection conn = JdbcUtils.getConnection();
//因为JdbcUtils.getConnection()的代码自己慢慢写的话也是有一些的，如下：
//因为db.properties文件是在src目录下的，而src目录下的内容都可以通过缓存拿到
static {
    try {
        //去拿到src路径下的db.properties文件资源
        InputStream inputStream = JdbcUtils.class.getClassLoader().getResourceAsStream("db.properties");
        Properties properties = new Properties();
        properties.load(inputStream);

        driver = properties.getProperty("driver");
        url = properties.getProperty("url");
        username = properties.getProperty("username");
        password = properties.getProperty("password");

        // 1.驱动只用加载一次
        Class.forName(driver);

    } catch (Exception e) {
        e.printStackTrace();
    }
}

//获取连接
public static Connection getConnection() throws SQLException {
    return DriverManager.getConnection(url,username,password);
}
```



#### DBCP

需要用到的jar包：commons-dbcp-1.4.jar、commons-pool-1.6.jar

跟dbcp相关的默认实现的数据源：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030618344545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)




<font color='cornflowerblue'>不同数据源在性能上是不同的，所以以后也会有性能更加优异的数据源不断出现！</font>

<font color='red'>**实际上，在上面的4.statement对象下的“如何获取到.properties中的数据用于数据库连接”中的代码就是DataSource所作的工作，所以，我们之前的配置实际上相当于自己实现了数据源，此时使用别人的数据源，实际上和我们上面的代码的整体思路是一样的，不同点在于别人的性能更高而已！**</font>



#### C390

需要用到的jar包

c3p0-0.9.5.5.jar、mchange-commons-java-0.2.19.jar



#### 结论：

无论使用什么数据源，本质是一样的！DataSource接口不会变，方法就不会变！