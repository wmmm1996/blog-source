---
title: 简单工厂模式-Simple Factory Pattern
date: 2019-01-15 10:20:24
tags:
  - java
  - 设计模式
categories: 
  - 理论学习
  - JAVA
  - 设计模式
---

# 概述

定义一个工厂类，它可以根据参数的不同返回不同类的实例，被创建的实例通常都具有共同的父类。因为在简单工厂模式中用于创建实例的方法是静态(static)方法，因此简单工厂模式又被称为静态工厂方法(Static Factory Method)模式。<!-- more -->

# 组成

- **工厂类角色**:这是本模式的核心,含有一定的商业逻辑和判断逻辑。在java中它往往由 一个具体类实现。(ProductFactory)
- **抽象产品角色**:它一般是具体产品继承的父类或者实现的接口。在java中由接口或者抽象类来实现。(Product)
- **具体产品角色**:工厂类所创建的对象就是此角色的实例。在java中由一个具体类实现。 来用类图来清晰的表示下的它们之间的关系。(ConcreteProductA,B,C)

# 适用场景

- 工厂类负责创建的对象比较少，由于创建的对象较少，不会造成工厂方法中的业务逻辑太过复杂
- 客户端只知道传入工厂类的参数，对于如何创建对象并不关心

# 案例

UML类图

![](https://i.loli.net/2019/01/14/5c3cac24ebe3f.png)

创建产品接口类:

```java
public interface Product {
}
```

创建三个具体产品类实现Product接口:

```java
public class ConcreteProductA implements Product {
}
public class ConcreteProductB implements Product {
}
public class ConcreteProductC implements Product {
}
```

生产产品的工厂类 (简单的工厂类/静态工厂类):

```java
public class ProductFactory {

    public static Product createProduct(String productType) {
        Product product = null;

        if ("A".equals(productType)) {
            product = new ConcreteProductA();
            System.out.println("工厂 创建了产品对象：ConcreteProductA");
        } else if ("B".equals(productType)) {
            product = new ConcreteProductB();
            System.out.println("工厂 创建了产品对象：ConcreteProductB");
        } else if ("C".equals(productType)) {
            product = new ConcreteProductC();
            System.out.println("工厂 创建了产品对象：ConcreteProductC");
        } else {
            System.out.println("没有该类型的产品，生产产品哪家强 ? 工厂方法模式  : 抽象工厂模式");
        }
        return product;
    }
}
```

客户端调用工厂类静态方法创建产品:

```java
public class Client {
    public static void main(String[] args) {
        ProductFactory.createProduct("A");
        ProductFactory.createProduct("C");
        ProductFactory.createProduct("B");
        ProductFactory.createProduct("D");
    }
}
```

输出结果:

```
工厂 创建了产品对象：ConcreteProductA
工厂 创建了产品对象：ConcreteProductC
工厂 创建了产品对象：ConcreteProductB
没有该类型的产品，生产产品哪家强 ? 工厂方法模式  : 抽象工厂模式
```

# 优缺点

## 优点

- 简单工厂模式实现了对象创建和使用的分离。
- 客户端无须知道所创建的具体产品类的类名，只需要知道具体产品类所对应的参数即可，对于一些复杂的类名，通过简单工厂模式可以在一定程度减少使用者的记忆量。
- 通过引入配置文件，可以在不修改任何客户端代码的情况下更换和增加新的具体产品类，在一定程度上提高了系统的灵活性。

## 缺点

- 由于工厂类集中了所有产品的创建逻辑，职责过重，一旦不能正常工作，整个系统都要受到影响。
- 使用简单工厂模式势必会增加系统中类的个数（引入了新的工厂类），增加了系统的复杂度和理解难度。
- 系统扩展困难，一旦添加新产品就不得不修改工厂逻辑，在产品类型较多时，有可能造成工厂逻辑过于复杂，不利于系统的扩展和维护。
- 简单工厂模式由于使用了静态工厂方法，造成工厂角色无法形成基于继承的等级结构。
