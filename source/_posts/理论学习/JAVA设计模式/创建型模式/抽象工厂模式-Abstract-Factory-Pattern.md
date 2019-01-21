---
title: 抽象工厂模式-Abstract Factory Pattern
date: 2019-01-15 10:23:46
tags:
  - java
  - 设计模式
categories: 
  - 理论学习
  - JAVA设计模式
---

# 概述
> 提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们具体的类。抽象工厂模式又称为Kit模式，它是一种对象创建型模式。

<!-- more -->

# 组成

- **抽象工厂(AbstractFactory)**
> 用于声明生成抽象产品的方法。
- **抽象产品(AbstractProduct)**
> 为每种产品声明接口，在抽象产品中定义了产品的抽象业务方法。
- **具体工厂(ConcreteFactory)**
> 实现了抽象工厂声明的生成抽象产品的方法，生成一组具体产品，这些产品构成了一个产品族，每一个产品都位于某个产品等级结构中。
- **具体产品(Product)**
> 定义具体工厂生产的具体产品对象，实现抽象产品接口中定义的业务方法。

# 适用场景

- 一个系统不应当依赖于产品类实例如何被创建、组合和表达的细节，这对于所有类型的工厂模式都是重要的。
- 系统中有多于一个的产品族，而每次只使用其中某一产品族。
- 属于同一个产品族的产品将在一起使用，这一约束必须在系统的设计中体现出来。
- 系统提供一个产品类的库，所有的产品以同样的接口出现，从而使客户端不依赖于具体实现。

# 案例

UML类图

![](https://i.loli.net/2019/01/14/5c3ca9cf6356e.png)

```java
//抽象工厂:
public abstract class AbstractFactory {
    abstract void createProductA();
    abstract void createProductB();
}
//抽象产品A,B
public abstract class AbstractProductA {
    abstract void use();
}
public abstract class AbstractProductB {
    abstract void eat();
}
//具体产品A1，A2，B1，B2
public class ProductA1 extends AbstractProductA {
    @Override
    void use() {
        System.out.println("产品A1的use方法");
    }
}
public class ProductA2 extends AbstractProductA {
    @Override
    void use() {
        System.out.println("产品A2的use方法");
    }
}
public class ProductB1 extends AbstractProductB {
    @Override
    void eat() {
        System.out.println("产品B1的eat方法");
    }
}
public class ProductB2 extends AbstractProductB {
    @Override
    void eat() {
        System.out.println("产品B2的eat方法");
    }
}
//具体工厂A,B
public class ConcreateFactory1 extends AbstractFactory {
    @Override
    AbstractProductA createProductA() {
        return new ProductA1();
    }

    @Override
    AbstractProductB createProductB() {
        return new ProductB1();
    }
}
public class ConcreateFactory2 extends AbstractFactory {
    @Override
    AbstractProductA createProductA() {
        return new ProductA2();
    }

    @Override
    AbstractProductB createProductB() {
        return new ProductB2();
    }
}
```

创建测试类:

```java
public class Test {
    public static void main(String[] args) {
        AbstractFactory factory1 = new ConcreateFactory1();
        AbstractFactory factory2 = new ConcreateFactory2();
        factory1.createProductA().use();
        factory1.createProductB().eat();
        factory2.createProductA().use();
        factory2.createProductB().eat();
    }
}
```

输出结果:

```
产品A1的use方法
产品B1的eat方法
产品A2的use方法
产品B2的eat方法
```

# 优缺点

## 优点

- 抽象工厂模式隔离了具体类的生成，使得客户并不需要知道什么被创建。
> 由于这种隔离，更换一个具体工厂就变得相对容易。所有的具体工厂都实现了抽象工厂中定义的那些公共接口，因此只需改变具体工厂的实例，就可以在某种程度上改变整个软件系统的行为。另外，应用抽象工厂模式可以实现高内聚低耦合的设计目的，因此抽象工厂模式得到了广泛的应用。
- 当一个产品族中的多个对象被设计成一起工作时，它能够保证客户端始终只使用同一个产品族中的对象。
> 这对一些需要根据当前环境来决定其行为的软件系统来说，是一种非常实用的设计模式。
- 增加新的具体工厂和产品族很方便，无须修改已有系统，符合“开闭原则”。

## 缺点

- 在添加新的产品对象时，难以扩展抽象工厂来生产新种类的产品
> 这是因为在抽象工厂角色中规定了所有可能被创建的产品集合，要支持新种类的产品就意味着要对该接口进行扩展，而这将涉及到对抽象工厂角色及其所有子类的修改，显然会带来较大的不便。
- 开闭原则的倾斜性
> 增加新的工厂和产品族容易，增加新的产品等级结构麻烦

# 练习
> Sunny软件公司欲推出一款新的手机游戏软件，该软件能够支持Symbian、Android和Windows Mobile等多个智能手机操作系统平台，针对不同的手机操作系统，该游戏软件提供了不同的游戏操作控制(OperationController)类和游戏界面控制(InterfaceController)类，并提供相应的工厂类来封装这些类的初始化过程。软件要求具有较好的扩展性以支持新的操作系统平台，为了满足上述需求，试采用抽象工厂模式对其进行设计。

```java
//游戏操作控制
public abstract class OperationControl {
    public abstract void operation();
}
//游戏界面控制
public abstract class InterfaceControl {
    public abstract void interfase();
}
//抽象操作控制类工厂
public abstract class OperationController {
    public abstract OperationControl symbian();
    public abstract OperationControl android();
    public abstract OperationControl windowsMobile();
}
//抽象界面控制类工厂
public abstract class InterfaceController {
    public abstract InterfaceControl symbian();
    public abstract InterfaceControl android();
    public abstract InterfaceControl windowsMobile();
}
//具体产品
public class SymbianInterfaceControl extends InterfaceControl {
    @Override
    public void interfase() {
        System.out.println("塞班界面控制");
    }
}
public class SymbianOperationControl extends OperationControl {
    @Override
    public void operation() {
        System.out.println("塞班操作控制");
    }
}
public class AndroidInterfaceControl extends InterfaceControl {
    @Override
    public void interfase() {
        System.out.println("安卓界面控制");
    }
}public class AndroidOperationControl extends OperationControl {
     @Override
     public void operation() {
         System.out.println("安卓操作控制");
     }
 }
 public class WindowsMobileInterfaceControl extends InterfaceControl {
     @Override
     public void interfase() {
         System.out.println("windows phone界面控制");
     }
 }

 public class WindowsMobileOperationControl extends OperationControl {
     @Override
     public void operation() {
         System.out.println("windows phone操作控制");
     }
 }
 //具体工厂
 public class ConcreateOperationController extends OperationController {
     @Override
     public OperationControl symbian() {
         return new SymbianOperationControl();
     }
 
     @Override
     public OperationControl android() {
         return new AndroidOperationControl();
     }
 
     @Override
     public OperationControl windowsMobile() {
         return new WindowsMobileOperationControl();
     }
 }
 public class ConcreateInterfaceController extends InterfaceController {
     @Override
     public InterfaceControl symbian() {
         return new SymbianInterfaceControl();
     }
 
     @Override
     public InterfaceControl android() {
         return new AndroidInterfaceControl();
     }
 
     @Override
     public InterfaceControl windowsMobile() {
         return new WindowsMobileInterfaceControl();
     }
 }
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        InterfaceController interfaceController = new ConcreateInterfaceController();
        interfaceController.android().interfase();
        OperationController operationController = new ConcreateOperationController();
        operationController.symbian().operation();
    }
}
```

输出结果:

```
安卓界面控制
塞班操作控制
```

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/abstractFactoryPattern)
👉 [返回设计模式概览](../../设计模式概览)