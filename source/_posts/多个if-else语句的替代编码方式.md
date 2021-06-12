---
title: 多个if/else语句的替代编码方式
date: 2021-03-28 18:50:11
categories:
- 小知识点
tags:
- 小知识点
---

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210324174251932.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODg3NzMz,size_16,color_FFFFFF,t_70#pic_center)

<!--less-->

---

### 概述
在写代码时，有时需要通过不同的type值，进行区分，针对不同的type，进行不同的操作。
如果以if/else的代码方式，则会显得很不美观，如下：
```java
if(type.equals("type1")){
	//todo
}
else if(type.equals("type2")){
	//todo
}
else if(type.equals("type3")){
	//todo
}
...
```
针对这种情况，很明显，对于`type1`、`type2`...`typeN`，比较好的方式是首先<font color=blue>**将type抽象为枚举类**</font><font color=red>（不管是字符串还是其他类型的值）</font>，然后再枚举类中编写相应的方法，通过循环遍历的方式，来区分当前是哪一种type。

<font color=red>**直接贴代码如下：**</font>下方的`of()`方法用来遍历并返回对应的枚举类型实例，避免了一个个if-else的判断语句。`isOf()`方法用来判断输入type值是否对应于枚举类中的某个值。`getTypeNo()`用来获取`TypeEnum`枚举类对应的值。


```java
public enum TypeEnum {
    Null(0), Type1(1), Type2(2),
    Type3(3), Type4(4),
    ;

    private Integer typeNo;

    TypeEnum(Integer typeNo){
        this.typeNo=typeNo;
    }

    /**
     * 此方法用于返回typeNo值所对应的枚举类实例
     * @param typeNo
     * @return
     */
    public static TypeEnum of(Integer typeNo){
        if(typeNo==null){
            return Null;
        }
        for(TypeEnum item : TypeEnum.values()){
            if(item.typeNo.equals(typeNo)){
                return item;
            }
        }
        return Null;
    }

    /**
     * 该方法用于判断typeNo值是否对应哪一个枚举值，对应则返回true，否则为false
     * @param typeNo
     * @return
     */
    public static TypeEnum isOf(Integer typeNo){
        if(typeNo==null){
            return false;
        }
        for(TypeEnum item : TypeEnum.values()){
            if(item.typeNo.equals(typeNo)){
                return true;
            }
        }
        return false;
    }
    
    public Integer getTypeNo(){
        return typeNo;
    }
}
```

<font color=red>**即用枚举类的方式替代多个if-else/switch-case语句块！使写出的代码更加简洁美观！**</font>