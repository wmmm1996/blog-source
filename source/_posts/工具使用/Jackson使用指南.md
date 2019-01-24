---
title: Jackson使用指南
date: 2019-01-21 20:12:32
tags:
  - jackson
categories: 
  - 工具使用
---

> 原教程链接[Jackson JSON Tutorial](https://www.baeldung.com/jackson)

从事JAVA开发工作以来,一直都离不开Jackson的序列化反序列化,对于Jackson的使用也一直处于够用但不深入的状态,知道看到这篇Jackson教程，下面是学完以后的总结。如有疏漏，欢迎指正。

<!-- more -->

# Jackson常用注解

## @JsonAnyGetter
> 像普通属性一样序列化Map

```java
public class ExtendableBean {
    public String name;
    private Map<String, String> properties;
 
    @JsonAnyGetter
    public Map<String, String> getProperties() {
        return properties;
    }
}
```

## @JsonGetter
> 将指定的方法标记为`getter`方法。可以用来代替`@JsonProperty`

```java
public class MyBean {
    public int id;
    private String name;
 
    @JsonGetter("name")
    public String getTheName() {
        return name;
    }
}
```

---
👉 [代码仓库](https://github.com/gcdd1993/Jackson-Guide-With-Samples)