---
title: Gson与文件处理
date: 2021-05-20 21:21:29
categories:
- json
tags:
- json
---

![](http://qiniusave.codeyu.cn/json.jpg)

<!--less-->

---

### 概述
1.介绍gson的使用，主要涉及序列化和反序列化
2.通过Gson将类对象转化为json字符串，并保存为本地文件
3.通过读取上传的MultipartFile文件，将json字符串反序列化为类对象
4.`org.apache.commons`扩展

### 1.Gson的使用
`Gson`是google的一个工具类，主要是利用其处理json数据。功能类似于jackson、fastjson等
使用`Gson`需要导入mavne依赖，如下：
```xml
<!-- https://mvnrepository.com/artifact/com.google.code.gson/gson -->
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.6</version>
</dependency>

```
1.将一个对象序列化和反序列化都是通过Gson对象的方法来实现的，所以新建一个Gson类对象的方法有：
（1）通过`new Gson()`方法来构建一个Gson类对象
```java
Gson gson = new Gson();
```

（2）通过GsonBuilder类来构建一个Gson类对象
```java
Gson gson = new GsonBuilder().create();
```
推荐使用方式（2）。


2.将一个类对象转化为json字符串的方法为：
```java
//可以指定对象类型，也可以不指定
Gson gson = new GsonBuilder().create();
DataEntity data = new DataEntity();
//1.不指定对象类型
String jsonString = gson.toJson(data);

//2.指定对象类型
String jsonString = gson.toJson(data, new TypeToken<DataEntity>(){}.getType());
```

3.将json字符串转化为指定对象
```java
//jsonString是json字符串
DataEntity dataEntity = gson.fromJson(jsonString, new TypeToken<DataEntity>(){}.getType());
```

### 2.将json字符串保存为本地文件
```java
//输入参数为json字符串和文件路径
private boolean transformToJsonFile(String jsonString, String filePath){
    boolean flag = true;
    try{
        File file = new File(filePath);

        if(!file.getParentFile().exists()){
            //若父目录不存在则创建父目录
            file.getParentFile().mkdirs();
        }

        if(file.exists()){
            //若文件存在，则删除旧文件
            file.delete();
        }

        file.createNewFile();

        //将格式化后的字符串写入文件
        Writer writer = new OutputStreamWriter(new FileOutputStream(file), StandardCharsets.UTF_8);
        writer.write(jsonString);
        writer.flush();
        writer.close();
    } catch (Exception e){
        flag = false;
        log.warn(Throwables.getStackTraceAsString(e));
    }
    return flag;
}
```

### 3.通过读取上传的MultipartFile文件，将json字符串反序列化为类对象
从`MultipartFile`文件读取`json`字符串的方法有：
（1）通过自定义流处理方法读取`json`数据，下述`readMultipartFileToJson()`方法即是。
```java
//将text文件中的数据以Json字符串的数据形式读出来
public String readMultipartFileToJson(MultipartFile multipartFile) throws IOException {
    BufferedReader bufferedReader = null;
    StringBuilder sb = new StringBuilder();
    try{
        bufferedReader = new BufferedReader(new InputStreamReader(multipartFile.getInputStream(), StandardCharsets.UTF_8));

        String tempString = null;
        while ((tempString = bufferedReader.readLine())!=null){
            sb.append(tempString);
        }
        bufferedReader.close();
    } catch (Exception e){
        log.warn(Throwables.getStackTraceAsString(e));
    } finally {
        if(bufferedReader!=null){
            try{
                bufferedReader.close();
            } catch (IOException e){
                log.warn(Throwables.getStackTraceAsString(e));
            }
        }
    }
    return sb.toString();
}
```
<font color=red>读取MultipartFile接口类文件中的数据与读取File类型文件数据是一样的，都是想办法获取`InputStream`，然后通过`BufferedReader`类对象的`readLine()`方法去读取即可。</font>
此处唯一需要注意的就是需要把所有的字符串都读出来作为总的String返回，再使用		`gson.fromJson()`方法进行反序列化操作！所以上述方法中使用了`StringBuilder`类来拼接从	`MultipartFile`中读取的每一行文本数据。

（2）通过`org.apache.commons.io`包下的工具类`IOUtils`中的`toString()`方法从`MultipartFile`文件读取`json`字符串。
`org.apache.commons.io`包的maven依赖为：
```xml
<!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.4</version>
</dependency>
```

```java
public static String toString(InputStream input, String encoding) throws IOException {
    return toString(input, Charsets.toCharset(encoding));
}
```
因为是static方法，在其他类中，该方法的调用形式为：
```java
String jsonString = IOUtils.toString(multipartFile.getInputStream(), StandardCharsets.UTF_8);
```
之后通过`gson`的`fromJson()`方法即可实现反序列操作！

### 4.`org.apache.commons`扩展
`org.apache.commons`是`apache`工具开发的一系列java工具包，包括：
`org.apache.commons.io`、`org.apache.commons.lang`、`org.apache.commons.fileupload`、`org.apache.commons.configuration2`、`org.apache.commons.collections`等
这些包所对应的依赖均可去[maven查询网站](https://mvnrepository.com/)进行查询！

此处仅简单的列举几个：
`org.apache.commons.io`
```xml
<!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.8.0</version>
</dependency>
```

`org.apache.commons.lang`
```xml
<!-- https://mvnrepository.com/artifact/org.apache.commons/commons-lang3 -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.12.0</version>
</dependency>
```
`org.apache.commons.fileupload`
```xml
<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.4</version>
</dependency>
```
等！