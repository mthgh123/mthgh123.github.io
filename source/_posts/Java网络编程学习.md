---
title: Java网络编程学习
date: 2021-03-14 18:31:28
categories:
- java
tags:
- java
---

![](http://qiniusave.codeyu.cn/java%E7%BD%91%E7%BB%9C%E7%BC%96%E7%A8%8B.jpg)

<!--less-->

---

## 网络编程

### 1.1概述 

**网络编程的目的：**实现资源共享和信息传递！

**想要实现信息传递/资源共享需要什么？**

javaweb：网页编程   属于B/S架构

网络编程：TCP/IP      属于C/S架构



### 1.2网络通信的要素

如何实现网络的通信？

通信双方地址：

- IP
- 端口号
- 如：192.165.1.102:59000



**规则：网络通信协议**

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021031319371299.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)


此处学习的网络编程重点是针对传输层的，即主要涉及到TCP/UDP协议！



**小结：**  

1.网络编程中有两个主要的问题

- 如何准确的定位到网络上的一台或者多台主机
- 找到主机之后如何进行通信

2.网络编程中的要素

- IP和端口号
- 网络通信协议

3.java中万物皆对象

- IP和端口相关的类
- TCP/UDP相关的类



### 1.3IP

Java中关于IP相关的类<font color='red'>**InetAddress**</font>，该类在java.net包下

关于IP地址：

- 能唯一定位一台网络上的计算机
- 本机的localhost的ip为：127.0.0.1

- IP地址的分类：
  - IP地址分类，如：IPV4/IPV6
    - IPV4：127.0.0.1（4个字节组成）		
    - IPV6：fe80::68a1:b503:f058:f1be%20（由8个无符号整数组成）
  - 公网（互联网使用）-私网（局域网）
    - 如：192.168.xxx.xxx：专门给组织内部使用的

可扩展了解一下：ABCD类地址https://zhidao.baidu.com/question/545595346.html



#### IP的测试小demo：

```java
//测试ip
public class TestInetAddress {
    public static void main(String[] args) {
        try {
            //查询本机ip地址
            InetAddress inetAddress1 = InetAddress.getByName("127.0.0.1");
            System.out.println(inetAddress1);

            InetAddress inetAddress3 = InetAddress.getByName("localhost");
            System.out.println(inetAddress3);

            InetAddress localHost = InetAddress.getLocalHost();
            System.out.println(localHost);

            //查询网站ip
            InetAddress inetAddress2 = InetAddress.getByName("www.baidu.com");
            System.out.println(inetAddress2);

            //常用方法
            System.out.println(Arrays.toString(inetAddress2.getAddress()));
            System.out.println(inetAddress2.getCanonicalHostName()); //规范的名字
            System.out.println(inetAddress2.getHostAddress()); //IP
            System.out.println(inetAddress2.getHostName()); //域名或者自己电脑的名字


        } catch (UnknownHostException e) {
            e.printStackTrace();
        }
    }
}
```



### 1.4端口

端口表示计算机上的一个程序的进程：

- 不同的进程有不同的端口号！用来区分软件！

- 端口号的范围为0-65535

- TCP/UDP的端口号的范围都是0-65535，所以同一个电脑上，TCP的端口号是80时，UDP的端口号也可以是80，但是同一个协议不能同时使用相同端口号

- 端口分类

  - 共有端口（0-1023）这些端口一般会被内置的进程使用，所以不要随意去使用这些端口

    - HTTP：80端口
    - HPPS：443端口
    - FTP：443端口

  - 程序注册端口：1024-49151（可分配给用户或程序）

    - tomcat：8080
    - Mysql：3306
    - Oracle：1521

  - 动态（私有的）：49152-65535（这里面的端口不好随便使用）

    ```bash
    #值得记住的命令行命令，可直接在命令行窗口中执行
    netstat -ano   #查看所有的端口
    
    netstat -ano|findstr "62767"	#查看指定的端口
    
    tasklist|findstr "10604"   #查找对应PID号的进程
    ```



java中关于端口的类是<font color='red'>**InetSocketAddress**</font>，包位置是java.net.InetSocketAddress

测试相关代码：

```java
import java.net.InetSocketAddress;

public class TestSocketAddress {
    public static void main(String[] args) {
        InetSocketAddress socketAddress1 = new InetSocketAddress("127.0.0.1", 8080);
        System.out.println(socketAddress1);

        InetSocketAddress socketAddress2 = new InetSocketAddress("localhost", 8080);
        System.out.println(socketAddress2);

        System.out.println(socketAddress1.getAddress());
        System.out.println(socketAddress1.getHostName());
        System.out.println(socketAddress1.getPort());
    }
}
```



### 1.5通信协议

**重要：**

- TCP：用户传输协议
- UDP：用户数据报协议



### 1.6TCP

#### 实现对话

编写两个类，一个是客户端类，一个是服务器类

客户端：

1.连接服务器Socket

2.发送消息



服务端：

1.建立服务端口

2.等待用户的连接，通过accept

3.接收用户的消息

```java
//客户端
public class TcpClientDemo01 {
    public static void main(String[] args) {

        try {
            //1.要知道服务器的地址
            InetAddress serverIP = InetAddress.getByName("127.0.0.1");
            //2.端口号
            int port = 9999;
            //3.创建一个socket连接（客户端则叫Socket）
            Socket socket = new Socket(serverIP,port);
            //4.发送消息
            OutputStream outputStream = socket.getOutputStream();
            outputStream.write("chichi超能吃的！".getBytes());

        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}

//服务端
public class TcpServerDemo01 {
    public static void main(String[] args) {

        try {
            //1.服务端有一个地址（服务端叫ServerSocket）
            ServerSocket serverSocket = new ServerSocket();
            //2.等待客户端连接过来，采用监听的方式
            Socket socket = serverSocket.accept();
            //3.读取客户端消息
            InputStream inputStream = socket.getInputStream();

            //4.讲inputStream数据打印输出

        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}
```

<font color='red'>**所以说socket是网络传输的关键，在上述的代码中，客户端的代码创建了一个socket：**</font>

```java
//3.创建一个socket连接（客户端则叫Socket）
Socket socket = new Socket(serverIP,port);
```

<font color='red'>**然后服务端的代码去接收这个socket，然后从socket中拿到数据：**</font>

```java
//2.等待客户端连接过来，采用监听的方式
Socket socket = serverSocket.accept();
//3.读取客户端消息(从socket中拿出数据)
InputStream inputStream = socket.getInputStream();
```



#### 文件上传

```java
//客户端
public class TcpClientDemo02 {
    public static void main(String[] args) throws Exception{
        //1.创建一个Socket连接
        Socket socket = new Socket(InetAddress.getByName("127.0.0.1"), 9000);
        //2.创建一个输出流
        OutputStream outputStream = socket.getOutputStream();

        //3.读取文件为FileInputStream
        FileInputStream fileInputStream = new FileInputStream(new File("beautfulview.jpg"));//该图片的路径是针对项目路径的
        //4.写出文件（这个写出文件的方式不推荐）
        byte[] buffer = new byte[1024];
        int len;
        while((len=fileInputStream.read(buffer))!=-1){
            outputStream.write(buffer,0,len);
        }

        //5.关闭资源
        fileInputStream.close();
        outputStream.close();
        socket.close();
    }
}

//服务端
public class TcpServerDemo02 {
    public static void main(String[] args) throws Exception{
        //1.创建服务
        ServerSocket serverSocket = new ServerSocket(9000);
        //2.监听客户端的连接
        Socket socket = serverSocket.accept();//阻塞式监听，会一直等待客户端连接
        //3.获取输入流
        InputStream inputStream = socket.getInputStream();

        //4.文件输出
        FileOutputStream receive = new FileOutputStream(new File("receive.jpg"));
        byte[] buffer = new byte[1024];
        int len;
        while ((len=inputStream.read(buffer))!=-1){
            receive.write(buffer,0,len);
        }

        //关闭资源
        receive.close();
        inputStream.close();
        socket.close();
        serverSocket.close();
    }
}
```



### 1.7Tomcat

服务端：

- 上述的1.6节中的服务端代码是我们自己写的
- Tomcat（是别人写好的服务端代码）<font color='red'>**（Java后台开发是用别人的服务器）**</font>

客户端：

- 自定义客户端 （C）
- 浏览器 （B）（别人写好的浏览器/客户端）

<font color='red'>当你启动tomcat服务器之后，通过在浏览器中键入localhost:8080/再加上相应的路径，然后键入回车即可访问到相应的内容，这就像相当于通过浏览器输入url，然后由tomcat进行处理，处理之后，将数据返回给浏览器的过程！</font>



### 1.8UDP

发短信：不用建立连接，但要直到对方地址！

#### 发送消息：

```java
//发送端
public class UdpClientDemo01 {
    //即使接收端没有实现运行，下方代码运行后也不会报错，因为UDP协议不需要建立连接，知识尽力发送而已
    public static void main(String[] args) throws Exception{
        //1.建立一个socket
        DatagramSocket socket = new DatagramSocket();

        //2.建个包
        String msg = "你好啊，吃吃！";
        //发送给谁
        InetAddress localhost = InetAddress.getByName("localhost");
        int port = 9090;
        //数据
        DatagramPacket packet = new DatagramPacket(msg.getBytes(), 0, msg.getBytes().length, localhost, port);

        //3.发个包
        socket.send(packet);

        //4.关闭流
        socket.close();
    }
}

//接收端
public class UdpServerDemo01 {
    public static void main(String[] args) throws Exception{
        //开放端口
        DatagramSocket socket = new DatagramSocket(9090);
        //接收数据包
        byte[] buffer = new byte[1024];
        DatagramPacket packet = new DatagramPacket(buffer, 0, buffer.length);//接收

        socket.receive(packet);
        System.out.println(packet.getAddress().getHostAddress());
        System.out.println(Arrays.toString(packet.getData()));
        System.out.println(new String(packet.getData(),0,packet.getLength()));
        //关闭连接
        socket.close();

    }
}
```





### 1.9URL

下载网络资源：

```java
import java.io.File;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class UrlDown {
    public static void main(String[] args) throws Exception{
        //1.下载地址
        URL url = new URL("http://localhost:8080/chichi/securityFile.txt");

        //2.连接到这个资源
        HttpURLConnection urlConnection = (HttpURLConnection) url.openConnection();

        InputStream inputStream = urlConnection.getInputStream();

        FileOutputStream fileOutputStream = new FileOutputStream(new File("download.txt"));
        byte[] buffer = new byte[1024];
        int len;
        while ((len=inputStream.read(buffer))!=-1){
            fileOutputStream.write(buffer,0,len); //写出这个数据
        }

        //关闭
        fileOutputStream.close();
        inputStream.close();
        urlConnection.disconnect();//断开连接
    }
}
```