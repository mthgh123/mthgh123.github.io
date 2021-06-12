---
title: MultipartFile与File类互转的封装工具类
date: 2021-04-02 20:11:25
categories:
- 小知识点
tags:
- 小知识点
---

![](http://qiniusave.codeyu.cn/springmvc.jpg)

<!--less-->

---

### 概述

由于业务需要，可能有时候需要将MultipartFile与File类进行互转，下方将二者互转的方法封装为工具类。

### 1.MultipartFile转化为File类
如果只是需要将MultipartFile转化为File类的话，不需要导入相关的mavern依赖，如下所示：
```java
import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.FileItemFactory;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.springframework.web.multipart.MultipartFile;

public class FileUtil {
	
	//将MultipartFile转化为File类的方法
	public static File MultipartFileToFile(MultipartFile multipartFile) throws IOException {
        File toFile = null;
        if (!multipartFile.equals("") && !(multipartFile.getSize() <= 0)) {
            InputStream ins = multipartFile.getInputStream();
            toFile = new File(multipartFile.getOriginalFilename());
            inputStreamToFile(ins, toFile);
            ins.close();
        }
        return toFile;
    }

    private static void inputStreamToFile(InputStream ins, File file) {
        try {
            OutputStream os = new FileOutputStream(file);
            int bytesRead = 0;
            byte[] buffer = new byte[8192];
            while ((bytesRead = ins.read(buffer, 0, 8192)) != -1) {
                os.write(buffer, 0, bytesRead);
            }
            os.close();
            ins.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
	
	//删除产生的临时文件的方法
    public static void deleteTempFile(File file){
        if(file!=null){
            File delFile = new File(file.toURI());
            delFile.delete();
        }
    }
}
```
测试代码如下：
```java
//multipartFile为MultipartFile文件
File file = FileUtil.MultipartFileToFile(multipartFile);
//=======接下来对file文件进行处理操作=======
//......

//将临时file文件删除
FileUtil.deleteTempFile(file);
```
### 2.File类转化为MultipartFile
如果需要将File转化为MultipartFile类的话，则需要先导入相关Maven依赖，如下：
```xml
<dependency>
	<groupId>commons-fileupload</groupId>
	<artifactId>commons-fileupload</artifactId>
	<version>1.4</version>
</dependency>
```
将File类转化为MultipartFile的工具类如下所示：
```java
import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.FileItemFactory;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.springframework.web.multipart.MultipartFile;

public class FileUtil {
	//将File类转化为MultipartFile的方法
	public static FileItem  getMultipartFile(File file, String fieldName){
        FileItemFactory factory = new DiskFileItemFactory(16, null);
        FileItem item = factory.createItem(fieldName, "text/plain", true, file.getName());
        int bytesRead = 0;
        byte[] buffer = new byte[8192];
        try {
            FileInputStream fis = new FileInputStream(file);
            OutputStream os = item.getOutputStream();
            while ((bytesRead = fis.read(buffer, 0, 8192)) != -1) {
                os.write(buffer, 0, bytesRead);
            }
            os.close();
            fis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return item;
    }
}
```
测试代码如下：
```java
//读取一个本地File文件
File file = new File("D:\Test\text.txt");
FileItem fileItem = FileUtil.getMultipartFile(file,"tempFileItem");
//转换为MultipartFile
MultipartFile multipartFile = new CommonsMultipartFile(fileItem);
```

### 3.将上述两个工具类整合为一个
```java
import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.FileItemFactory;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.springframework.web.multipart.MultipartFile;

public class FileUtil {
	public static File MultipartFileToFile(MultipartFile multipartFile) throws IOException {
        File toFile = null;
        if (!multipartFile.equals("") && !(multipartFile.getSize() <= 0)) {
            InputStream ins = multipartFile.getInputStream();
            toFile = new File(multipartFile.getOriginalFilename());
            inputStreamToFile(ins, toFile);
            ins.close();
        }
        return toFile;
    }

    private static void inputStreamToFile(InputStream ins, File file) {
        try {
            OutputStream os = new FileOutputStream(file);
            int bytesRead = 0;
            byte[] buffer = new byte[8192];
            while ((bytesRead = ins.read(buffer, 0, 8192)) != -1) {
                os.write(buffer, 0, bytesRead);
            }
            os.close();
            ins.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void deleteTempFile(File file){
        if(file!=null){
            File delFile = new File(file.toURI());
            delFile.delete();
        }
    }

	public static FileItem  getMultipartFile(File file, String fieldName){
        FileItemFactory factory = new DiskFileItemFactory(16, null);
        FileItem item = factory.createItem(fieldName, "text/plain", true, file.getName());
        int bytesRead = 0;
        byte[] buffer = new byte[8192];
        try {
            FileInputStream fis = new FileInputStream(file);
            OutputStream os = item.getOutputStream();
            while ((bytesRead = fis.read(buffer, 0, 8192)) != -1) {
                os.write(buffer, 0, bytesRead);
            }
            os.close();
            fis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return item;
    }
}
```