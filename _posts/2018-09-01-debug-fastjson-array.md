---
layout:     post
title:      BUG实录 - fastjson JSONArray.parseArray 异常
subtitle:   老版本 fastjson JSONArray.parseArray 失效
date:       2018-09-01
author:     壹芝
header-img: img/post-bg-debug.png
catalog: true
tags:
    - Fastjson
    - Java
    - Bug
    - JSONArray
---

## fastjson JSONArray.parseArray 错误

###### 整理自本人的有道历史云笔记

### 简介
> fastjson用于将Java Bean序列化为JSON字符串,也可以从JSON字符串反序列化到JavaBean  
之前使用过程中从来没有遇到过错误的情况,这次的情况不同,使用过程中发现老版本的fastjson的一个反序列化的BUG

### 二. 问题发现
> 公司某项目中接口使用json交互,一直运行正常,客户端部分接口上传的是JSONArray,项目发布之后部分接口出现异常  
还原代码如下,实际代码比例子中的复杂很多

有错误的 fastjson 版本
```
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.7</version>
</dependency>

```

服务端版本发布前代码
```
@Getter
@Setter
public class Clot {
private String name;
}

@Getter
@Setter
public class ClotHeme extends Clot{
 private String level;
 private String icon;
}
 ```

新版本代码
```
@Getter
@Setter
@Accessors(chain = true)
public class Clot {
private String name;
}

@Getter
@Setter
public class ClotHeme extends Clot{
 private String level;
 private String icon;
}
```

反序列化代码
``` 
List<ClotHeme> a = JSONArray.parseArray("[{\"name\":\"Cili\"}]",ClotHeme.class);
// name 字段丢失
```

### 三. 问题分析

> 代码修改前后之变化了一行 ``` @Accessors(chain = true) ```  
类似效果如下,set之后返回this

```
public static class Clot {
    private String name;

    public String getName() {
        return name;
    }

    public Clot setName(String name) {
        this.name = name;
        return this;
    }
}
```

> fasjson 在发序列化对象的时候先遍历Class里面的全部字段,遍历方法是取出setXxx方法,去掉set变成Xxx,然后对应到字段xxx

有BUG的代码如下
```
com.alibaba.fastjson.util.DeserializeBeanInfo#computeSetters

 ......
 for (Method method : clazz.getMethods()) {
    int ordinal = 0, serialzeFeatures = 0;
    String methodName = method.getName();
    if (methodName.length() < 4) {
        continue;
    }

    if (Modifier.isStatic(method.getModifiers())) {
        continue;
    }

    // support builder set
    if (!(method.getReturnType().equals(Void.TYPE) || method.getReturnType().equals(clazz))) {
        continue;
    }

    if (method.getParameterTypes().length != 1) {
        continue;
    }

    JSONField annotation = method.getAnnotation(JSONField.class);

    if (annotation == null) {
        annotation = TypeUtils.getSupperMethodAnnotation(clazz, method);
    }

    if (annotation != null) {
        if (!annotation.deserialize()) {
            continue;
        }

        ordinal = annotation.ordinal();
        serialzeFeatures = SerializerFeature.of(annotation.serialzeFeatures());

        if (annotation.name().length() != 0) {
            String propertyName = annotation.name();
            beanInfo.add(new FieldInfo(propertyName, method, null, clazz, type, ordinal, serialzeFeatures));
            TypeUtils.setAccessible(method);
            continue;
        }
    }

    if (!methodName.startsWith("set")) {
        continue;
    }
    ......
```

> 其中以下代码限制了setXxx方法是必须返回类型必须是 Void.TYPE || 和本身相等 而没有判断是否是父类
```
// support builder set
if (!(method.getReturnType().equals(Void.TYPE) || method.getReturnType().equals(clazz))) {
    continue;
}
```

### 三. 解决方法

##### 1. 删除新增代码 
> ~~@Accessors(chain = true)~~

##### 2. 升级 fastjson 版本
```
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.48</version>
</dependency>
```


