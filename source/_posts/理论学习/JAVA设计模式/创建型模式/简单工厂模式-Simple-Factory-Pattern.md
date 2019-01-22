---
title: 简单工厂模式-Simple Factory Pattern
date: 2019-01-15 10:20:24
tags:
  - java
  - 设计模式
categories: 
  - 理论学习
  - JAVA设计模式
---

# 概述
> 定义一个工厂类，它可以根据参数的不同返回不同类的实例，被创建的实例通常都具有共同的父类。因为在简单工厂模式中用于创建实例的方法是静态(static)方法，因此简单工厂模式又被称为静态工厂方法(Static Factory Method)模式。

<!-- more -->

# 组成

- 工厂类角色
> 这是本模式的核心,含有一定的商业逻辑和判断逻辑。在java中它往往由 一个具体类实现。(ProductFactory)
- 抽象产品角色
> 它一般是具体产品继承的父类或者实现的接口。在java中由接口或者抽象类来实现。(Product)
- 具体产品角色
> 工厂类所创建的对象就是此角色的实例。在java中由一个具体类实现。 来用类图来清晰的表示下的它们之间的关系。(ConcreteProductA,B,C)

# 适用场景

- 工厂类负责创建的对象比较少，由于创建的对象较少，不会造成工厂方法中的业务逻辑太过复杂
- 客户端只知道传入工厂类的参数，对于如何创建对象并不关心

# 案例

UML类图

![](https://i.loli.net/2019/01/14/5c3cac24ebe3f.png)

```java
//产品接口类
public interface Product {
}
//三个具体产品类实现Product接口
public class ConcreteProductA implements Product {
}
public class ConcreteProductB implements Product {
}
public class ConcreteProductC implements Product {
}
//生产产品的工厂类 (简单的工厂类/静态工厂类)
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

客户端:

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

## 缺点

- 工厂类职责过重
> 由于工厂类集中了所有产品的创建逻辑，职责过重，一旦不能正常工作，整个系统都要受到影响。
- 增加了系统的复杂度和理解难度
> 使用简单工厂模式势必会增加系统中类的个数（引入了新的工厂类）。
- 系统扩展困难
> 一旦添加新产品就不得不修改工厂逻辑，在产品类型较多时，有可能造成工厂逻辑过于复杂，不利于系统的扩展和维护。
- 工厂角色无法形成基于继承的等级结构
> 这是由于简单工厂模式使用了静态工厂方法

# 练习
> 使用简单工厂模式设计一个可以创建不同几何形状（如圆形、方形和三角形等）的绘图工具，每个几何图形都具有绘制draw()和擦除erase()两个方法，要求在绘制不支持的几何图形时，提示一个UnSupportedShapeException。

## UML类图

![](https://i.imgur.com/ONC2sEF.png)

## 代码

```java
//几何图形接口
public interface Shape {
    //绘制
    void draw();
    //擦除
    void erase();
}
//简单工厂
public class ShapeFactory {
    public static Shape createShape(String shapeStr) {
        Shape shape;
        switch (shapeStr) {
            case "circle": {
                shape = new CircleShape();
            }
            break;
            case "square": {
                shape = new SquareShape();
            }
            break;
            case "triangle": {
                shape = new TriangleShape();
            }
            break;
            default:
                throw new UnSupportedShapeException("不支持的几何图形");
        }
        return shape;
    }
}
//具体类
public class CircleShape implements Shape {
    @Override
    public void draw() {
        System.out.println("绘制圆形");
    }

    @Override
    public void erase() {
        System.out.println("擦除圆形");
    }
}
public class SquareShape implements Shape {

    @Override
    public void draw() {
        System.out.println("绘制方形");
    }

    @Override
    public void erase() {
        System.out.println("擦除方形");
    }
}
public class TriangleShape implements Shape {
    @Override
    public void draw() {
        System.out.println("绘制三角形");
    }

    @Override
    public void erase() {
        System.out.println("擦除三角形");
    }
}
```

建立一个UnSupportedShapeException

```java
public class UnSupportedShapeException extends RuntimeException {
    public UnSupportedShapeException(String message) {
        super(message);
    }
}
```

客户端

```java
public class Client {
    public static void main(String[] args) {
        Shape circle = ShapeFactory.createShape("circle");
        circle.draw();
        circle.erase();
        Shape shape = ShapeFactory.createShape("111");
        shape.draw();
        shape.erase();
    }
}
```

输出结果:

```
绘制圆形
擦除圆形
Exception in thread "main" simpleFactoryPattern.exercise.UnSupportedShapeException: 不支持的几何图形
	at simpleFactoryPattern.exercise.ShapeFactory.createShape(ShapeFactory.java:25)
	at simpleFactoryPattern.exercise.Client.main(Client.java:12)
```

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/simpleFactoryPattern)
👉 [返回设计模式概览](../../设计模式概览)