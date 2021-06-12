---
title: 解析key值不确定的json数据
date: 2021-03-26 14:47:07
categories:
- json
tags:
- json
---

![](http://qiniusave.codeyu.cn/json.jpg)

<!--less-->

---

### 概述
之前在使用json解析数据时，都是key为固定已知的情况，这种情况比较好解析。而今天碰到一种key不固定的情况，且key,value键值对个数也是不固定的，如下所示：
可能返回的json数据是这样的：


```json
"nameResult":{
    "name1":"张三",
    "name2":"李四"
}
```

也有可能是这样的：

```json
"nameResult":{
    "name5":"王五",
    "name8":"旺七",
    "name6":"二狗"
}
```
### 对于这种key不确定，且key,value键值对的个数也不确定的json数据如何解析？
看了其他很多人的博客，他们列举的解析方法为：
```java
//jsonString为原生的json数据
JSONObject jsonObject = new JSONObject(jsonString);
//通过迭代器获取这段json当中所有的key值
Iterator keys = jsonObject.keys();
//然后通过一个循环取出所有的key值
while (keys.hasNext()){
	String key = String.valueOf(keys.next());
//获取了key然后再解析
```
<font color=red>**这种方法我去尝试过，但是发现JsonObject对象没有key()方法，只有entrySet()方法，如下图所示：**</font>
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210326102330344.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)
<font color=red>**所以，采用entrySet()方法进行解析，并解析成功！**</font>

#### 正确解析方法为：
还是拿这个json数据来举例：
```json
//下方json数据作为下述解析代码的jsonString，是String类型的数据
{
    "name5":"王五",
    "name8":"旺七",
    "name6":"二狗"
}
```
<font color=red>**解析代码为：**</font>


```java
//jsonString为输入的json字符串，是String类型的数据
JsonParser jsonParser = new JsonParser();
JsonElement jsonElement = jsonParser.parse(jsonString);//先解析jsonString
JsonObject jsonObject = jsonElement.getAsJsonObject();
if(jsonObject!=null){
    /*=========解析不确定key,value键值对的代码从这里开始=========*/
    //得到所有的键值对的entry，即entrySet
    Set<Map.Entry<String, JsonElement>> entrySet = jsonObject.entrySet();
    //for循环遍历所有的entry
    for(Map.Entry<String, JsonElement> entry : entrySet){
        //entry有两个方法，getKey()为得到key，getValue()为得到value
        //此处的getValue()会得到value值，即例子中的"王五"/"旺七"/"二狗"...
        JsonObject jsonObject = entry.getValue().getAsJsonObject();
        //根据需求对jsonObject进行进一步处理即可
    }

```

可通过StringBuilder将结果打印出来：

```java
//jsonString为输入的json字符串，是String类型的数据
JsonParser jsonParser = new JsonParser();
JsonElement jsonElement = jsonParser.parse(jsonString);//先解析jsonString
JsonObject jsonObject = nameElement.getAsJsonObject();
if(jsonObject!=null){
    Set<Map.Entry<String, JsonElement>> entrySet = jsonObject.entrySet();
    StringBuilder stringbuilder = new StringBuilder();
    //for循环遍历所有的entry
    for(Map.Entry<String, JsonElement> entry : entrySet){
		String name = entry.getkey();
        stringbuilder.append(name).append(":");
        String namevalue = entry.getValue().getAsString();
        stringbuilder.append(namevalue).append(" | ");
    }
//最终输出结果为：name5:王五 | name8:旺七 | name6:二狗 |
```