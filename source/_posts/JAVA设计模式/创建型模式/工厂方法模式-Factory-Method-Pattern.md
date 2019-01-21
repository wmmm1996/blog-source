---
title: 工厂方法模式-Factory Method Pattern
date: 2019-01-15 10:22:31
tags:
  - java
  - 设计模式
categories: 
  - JAVA设计模式
---

# 概述
> 工厂方法模式，又称工厂模式、多态工厂模式和虚拟构造器模式，通过定义工厂父类负责定义创建对象的公共接口，而子类则负责生成具体的对象。

<!-- more -->

# 组成

- 抽象产品(Product)
> 它是定义产品的接口，是工厂方法模式所创建对象的超类型，也就是产品对象的公共父类。
- 抽象工厂(Factory)
> 在抽象工厂类中，声明了工厂方法(Factory Method)，用于返回一个产品。抽象工厂是工厂方法模式的核心，所有创建对象的工厂类都必须实现该接口。
- 具体产品(ConcreteProduct)
> 它实现了抽象产品接口，某种类型的具体产品由专门的具体工厂创建，具体工厂和具体产品之间一一对应。
- 具体工厂(ConcreteFactory)
> 它是抽象工厂类的子类，实现了抽象工厂中定义的工厂方法，并可由客户端调用，返回一个具体产品类的实例。

# 适用场景

- 当一个类不知道它所需要的对象的类时
- 当一个类希望通过其子类来指定创建对象时
- 将创建对象的任务委托给多个工厂子类中的某一个，客户端在使用时可以无须关心是哪一个工厂子类创建产品子类，需要时再动态指定，可将具体工厂类的类名存储在配置文件或数据库中。

# 案例

UML类图

![](https://i.loli.net/2019/01/14/5c3ca9c875a43.png)

```java
//抽象工厂类，定义具体工厂的公共接口
public abstract class AbstractProductFactory {
    public abstract BaseProduct Manufacture();
}
//抽象产品类 ，定义具体产品的公共接口
public abstract class BaseProduct {
    public abstract void Show();
}
//具体产品类（继承抽象产品类）,定义生产的具体产品
public class ProductA extends BaseProduct {
    @Override
    public void Show() {
        System.out.println("生产出了产品A");
    }
}
public class ProductB extends BaseProduct {
    @Override
    public void Show() {
        System.out.println("生产出了产品B");
    }
}
//具体工厂类（继承抽象工厂类）,定义创建对应具体产品实例的方法
public class FactoryA extends AbstractProductFactory {
    @Override
    public BaseProduct Manufacture() {
        return new ProductA();
    }
}
public class FactoryB extends AbstractProductFactory {
    @Override
    public BaseProduct Manufacture() {
        return new ProductB();
    }
}
```

外界通过调用具体工厂类的方法，从而创建不同具体产品类的实例:

```java
public class Test {
    public static void main(String[] args) {
        //客户要产品A
        FactoryA mFactoryA = new FactoryA();
        mFactoryA.Manufacture().Show();

        //客户要产品B
        FactoryB mFactoryB = new FactoryB();
        mFactoryB.Manufacture().Show();
    }
}
```

输出结果:

```
生产出了产品A
生产出了产品B
```

# 优缺点

## 优点

- 更符合开-闭原则
> 新增一种产品时，只需要增加相应的具体产品类和相应的工厂子类即可
- 符合单一职责原则
> 每个具体工厂类只负责创建对应的产品
- 不使用静态工厂方法，可以形成基于继承的等级结构

## 缺点

- 增加了系统的复杂度以及额外的开销
> 在添加新产品时，需要编写新的具体产品类，而且还要提供与之对应的具体工厂类，系统中类的个数将成对增加，在一定程度上增加了系统的复杂度，有更多的类需要编译和运行，会给系统带来一些额外的开销。
- 增加了系统的实现难度
> 由于考虑到系统的可扩展性，需要引入抽象层，在客户端代码中均使用抽象层进行定义，增加了系统的抽象性和理解难度，且在实现时可能需要用到DOM、反射等技术，增加了系统的实现难度。

# 练习
> 使用工厂方法模式设计一个程序来读取各种不同类型的图片格式，针对每一种图片格式都设计一个图片读取器，如GIF图片读取器用于读取GIF格式的图片、JPG图片读取器用于读取JPG格式的图片。需充分考虑系统的灵活性和可扩展性。

# UML类图

![](https://i.imgur.com/JvS5qW4.png)

```java
//抽象读取器
public abstract class AbstractReader {
    public abstract void read();
}
//抽象读取器工厂
public abstract class AbstractReaderFactory {
    public abstract AbstractReader createReader();
}
//JPG读取器(具体读取器)
public class JPGReader extends AbstractReader {
    @Override
    public void read() {
        System.out.println("使用JPG读取器读取图片");
    }
}
//GIF读取器(具体读取器)
public class GIFReader extends AbstractReader {
    @Override
    public void read() {
        System.out.println("使用GIF读取器读取图片");
    }
}
//JPG读取器工厂(具体工厂)
public class JPGReaderFactory extends AbstractReaderFactory {
    @Override
    public AbstractReader createReader() {
        return new JPGReader();
    }
}
//GIF读取器工厂(具体工厂)
public class GIFReaderFactory extends AbstractReaderFactory {
    @Override
    public AbstractReader createReader() {
        return new GIFReader();
    }
}
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        AbstractReaderFactory readerFactory = new JPGReaderFactory();
        readerFactory.createReader().read();
        readerFactory = new GIFReaderFactory();
        readerFactory.createReader().read();
    }
}
```

输出结果:

```
使用JPG读取器读取图片
使用GIF读取器读取图片
```

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/factoryMethodPattern)
👉 [返回设计模式概览](../../设计模式概览)