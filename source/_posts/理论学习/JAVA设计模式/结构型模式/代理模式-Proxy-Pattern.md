---
title: 代理模式-Proxy Pattern
date: 2019-01-16 11:24:04
tags:
  - java
  - 设计模式
categories: 
  - 理论学习
  - JAVA设计模式
---

# 概述
> 在代理模式中引入了一个新的代理对象，代理对象在客户端对象和目标对象之间起到中介的作用，它去掉客户不能看到的内容和服务或者增添客户需要的额外的新服务。

<!-- more -->

# 组成

- Subject（抽象主题角色）
> 它声明了真实主题和代理主题的共同接口，这样一来在任何使用真实主题的地方都可以使用代理主题，客户端通常需要针对抽象主题角色进行编程。
- Proxy（代理主题角色）
> 它包含了对真实主题的引用，从而可以在任何时候操作真实主题对象。通常，在代理主题角色中，客户端在调用所引用的真实主题操作之前或之后还需要执行其他操作，而不仅仅是单纯调用真实主题对象中的操作。
- RealSubject（真实主题角色）
> 它定义了代理角色所代表的真实对象，在真实主题角色中实现了真实的业务操作，客户端可以通过代理主题角色间接调用真实主题角色中定义的操作。

# 适用场景

- 远程代理
> 为位于两个不同地址空间对象的访问提供了一种实现机制，可以将一些消耗资源较多的对象和操作移至性能更好的计算机上，提高系统的整体运行效率。
- 虚拟代理
> 通过一个消耗资源较少的对象来代表一个消耗资源较多的对象，可以在一定程度上节省系统的运行开销。
- 缓冲代理
> 为某一个操作的结果提供临时的缓存存储空间，以便在后续使用中能够共享这些结果，优化系统性能，缩短执行时间。
- 保护代理
> 可以控制对一个对象的访问权限，为不同用户提供不同级别的使用权限。

# 案例

```java
//身份验证类，业务类，它提供方法Validate()来实现身份验证
public class AccessValidator {
    public boolean validate(String userId) {
        System.out.println("在数据库中验证用户'" + userId + "'是否是合法用户？");
        if ("杨过".equals(userId)) {
            System.out.println(userId + "登录成功！");
            return true;
        } else {
            System.out.println(userId + "登录失败！");
            return false;
        }
    }
}
//日志记录类，业务类，它提供方法Log()来保存日志
public class Logger {
    public void log(String userId) {
        System.out.println("更新数据库，用户" + userId + "查询次数加1！");
    }
}
//抽象查询类
public interface Searcher {
    String doSearch(String userId, String keyword);
}
//具体查询类
public class RealSearcher implements Searcher {
    @Override
    public String doSearch(String userId, String keyword) {
        System.out.println("用户" + userId + "使用关键词" + keyword + "查询商务信息！");
        return "返回具体内容";
    }
}
//代理查询类,在具体查询之前执行一些业务逻辑，达到增强该方法的目的
public class ProxySearcher implements Searcher {
    private RealSearcher searcher = new RealSearcher(); //维持一个对真实主题的引用
    private AccessValidator validator;
    private Logger logger;

    @Override
    public String doSearch(String userId, String keyword) {
        if (this.validate(userId)) {
            String result = searcher.doSearch(userId, keyword); //调用真实主题对象的查询方法
            this.log(userId); //记录查询日志
            return result; //返回查询结果
        }
        return null;
    }

    //创建访问验证对象并调用其Validate()方法实现身份验证
    public boolean validate(String userId) {
        validator = new AccessValidator();
        return validator.validate(userId);
    }

    //创建日志记录对象并调用其Log()方法实现日志记录
    public void log(String userId) {
        logger = new Logger();
        logger.log(userId);
    }
}
```

客户端:

```java
public class ProxySample {
    public static void main(String[] args) {
        Searcher searcher = new ProxySearcher();
        searcher.doSearch("杨过", "玉女心经");
        searcher.doSearch("小龙女", "玉女心经");
    }
}
```

输出结果:

```
在数据库中验证用户'杨过'是否是合法用户？
杨过登录成功！
用户杨过使用关键词玉女心经查询商务信息！
更新数据库，用户杨过查询次数加1！
在数据库中验证用户'小龙女'是否是合法用户？
小龙女登录失败！
```

本实例是保护代理和智能引用代理的应用实例，在代理类ProxySearcher中实现对真实主题类的权限控制和引用计数，如果需要在访问真实主题时增加新的访问控制机制和新功能，只需增加一个新的代理类，再修改配置文件，在客户端代码中使用新增代理类即可，源代码无须修改，符合开闭原则。

# 优缺点

## 优点

- 降低了系统的耦合度
> 能够协调调用者和被调用者，在一定程度上降低了系统的耦合度。
- 系统具有较好的灵活性和可扩展性
> 客户端可以针对抽象主题角色进行编程，增加和更换代理类无须修改源代码，符合开闭原则，系统具有较好的灵活性和可扩展性。

## 缺点

- 由于在客户端和真实主题之间增加了代理对象，因此有些类型的代理模式可能会造成请求的处理速度变慢，例如保护代理。
- 实现代理模式需要额外的工作，而且有些代理模式的实现过程较为复杂，例如远程代理。

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/proxyPattern)
👉 [返回设计模式概览](../../设计模式概览)