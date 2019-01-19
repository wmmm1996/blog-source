---
title: 策略模式-Strategy Pattern
date: 2019-01-19 20:03:11
tags:
  - java
  - 设计模式
categories: 
  - JAVA设计模式
---

# 概述

> 定义一系列算法类，将每一个算法封装起来，并让它们可以相互替换，策略模式让算法独立于使用它的客户而变化，也称为政策模式(Policy)。

<!-- more -->

# 组成

- Context（环境类）
> 环境类是使用算法的角色，它在解决某个问题（即实现某个方法）时可以采用多种策略。在环境类中维持一个对抽象策略类的引用实例，用于定义所采用的策略。
- Strategy（抽象策略类）
> 它为所支持的算法声明了抽象方法，是所有策略类的父类，它可以是抽象类或具体类，也可以是接口。环境类通过抽象策略类中声明的方法在运行时调用具体策略类中实现的算法。
- ConcreteStrategy（具体策略类）
> 它实现了在抽象策略类中声明的算法，在运行时，具体策略类将覆盖在环境类中定义的抽象策略类对象，使用一种具体的算法实现某个业务处理。

# 适用场景

-  一个系统需要动态地在几种算法中选择一种
> 一个系统需要动态地在几种算法中选择一种，那么可以将这些算法封装到一个个的具体算法类中，而这些具体算法类都是一个抽象算法类的子类。换言之，这些具体算法类均有统一的接口，根据“里氏代换原则”和面向对象的多态性，客户端可以选择使用任何一个具体算法类，并只需要维持一个数据类型是抽象算法类的对象。
- 一个对象有很多的行为
> 一个对象有很多的行为，如果不用恰当的模式，这些行为就只好使用多重条件选择语句来实现。此时，使用策略模式，把这些行为转移到相应的具体策略类里面，就可以避免使用难以维护的多重条件选择语句。
- 不希望客户端知道复杂的、与算法相关的数据结构
> 在具体策略类中封装算法与相关的数据结构，可以提高算法的保密性与安全性。

# 案例

电影院打折方案

```java
//电影票类：环境类
@Data
public class MovieTicket {
    private double price;
    private Discount discount; //维持一个对抽象折扣类的引用

    public double getPrice() {
        //调用折扣类的折扣价计算方法
        return discount.calculate(this.price);
    }
}
//折扣类：抽象策略类
public interface Discount {
    double calculate(double price);
}
//学生票折扣类：具体策略类
public class StudentDiscount implements Discount {
    @Override
    public double calculate(double price) {
        System.out.println("学生票：");
        return price * 0.8;
    }
}
//儿童票折扣类：具体策略类
public class ChildrenDiscount implements Discount {
    @Override
    public double calculate(double price) {
        System.out.println("儿童票：");
        return price - 10;
    }
}
//VIP会员票折扣类：具体策略类
public class VIPDiscount implements Discount {
    @Override
    public double calculate(double price) {
        System.out.println("VIP票：");
        System.out.println("增加积分！");
        return price * 0.5;
    }
}
```

客户端：

```java
public class Client {
    public static void main(String[] args) {
        MovieTicket mt = new MovieTicket();
        double originalPrice = 60.0;

        mt.setPrice(originalPrice);
        System.out.println("原始价为：" + originalPrice);
        System.out.println("---------------------------------");

        Discount discount = new VIPDiscount();
        mt.setDiscount(discount); //注入折扣对象

        double currentPrice = mt.getPrice();
        System.out.println("折后价为：" + currentPrice);
    }
}
```

输出结果：

```
原始价为：60.0
---------------------------------
VIP票：
增加积分！
折后价为：30.0
```

# 优缺点

## 优点

- 策略模式提供了对“开闭原则”的完美支持
> 用户可以在不修改原有系统的基础上选择算法或行为，也可以灵活地增加新的算法或行为。
- 策略模式提供了管理相关的算法族的办法
> 策略类的等级结构定义了一个算法或行为族，恰当使用继承可以把公共的代码移到抽象策略类中，从而避免重复的代码。
- 提供了一种可以替换继承关系的办法
- 可以避免多重条件选择语句
- 提供了一种算法的复用机制
> 由于将算法单独提取出来封装在策略类中，因此不同的环境类可以方便地复用这些策略类。

# 缺点

- 客户端必须知道所有的策略类，并自行决定使用哪一个策略类。
> 这就意味着客户端必须理解这些算法的区别，以便适时选择恰当的算法。换言之，策略模式只适用于客户端知道所有的算法或行为的情况。
- 将造成系统产生很多具体策略类
> 任何细小的变化都将导致系统要增加一个新的具体策略类。
- 无法同时在客户端使用多个策略类
> 也就是说，在使用策略模式时，客户端每次只能使用一个策略类，不支持使用一个策略类完成部分功能后再使用另一个策略类来完成剩余功能的情况。

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/strategyPattern)
👉 [返回设计模式概览](../../设计模式概览)