---
title: 外观模式-Facade Pattern
date: 2019-01-15 23:19:55
tags:
  - java
  - 设计模式
categories: 
  - 理论学习
  - JAVA
  - 设计模式
---

# 概述

为子系统中的一组接口提供一个统一的入口。外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。 <!-- more -->

# 组成

- **Facade（外观角色）**：在客户端可以调用它的方法，在外观角色中可以知道相关的（一个或者多个）子系统的功能和责任；在正常情况下，它将所有从客户端发来的请求委派到相应的子系统去，传递给相应的子系统对象处理。
- **SubSystem（子系统角色）**：在软件系统中可以有一个或者多个子系统角色，每一个子系统可以不是一个单独的类，而是一个类的集合，它实现子系统的功能；每一个子系统都可以被客户端直接调用，或者被外观角色调用，它处理由外观类传过来的请求；子系统并不知道外观的存在，对于子系统而言，外观角色仅仅是另外一个客户端而已。

# 适用场景

- 当要为访问一系列复杂的子系统提供一个简单入口时可以使用外观模式。
- 客户端程序与多个子系统之间存在很大的依赖性。引入外观类可以将子系统与客户端解耦，从而提高子系统的独立性和可移植性。
- 在层次化结构中，可以使用外观模式定义系统中每一层的入口，层与层之间不直接产生联系，而通过外观类建立联系，降低层之间的耦合度。

# 案例

UML类图

![](https://i.imgur.com/pitBuDK.png)

子系统类通常是一些业务类，实现了一些具体的、独立的业务功能:

```java
public class SubSystemA {
    public void MethodA() {
        System.out.println("我是子系统A");
    }
}
public class SubSystemB {
    public void MethodB() {
        System.out.println("我是子系统B");
    }
}
public class SubSystemC {
    public void MethodC() {
        System.out.println("我是子系统C");
    }
}
```

外观类:

```java
public class Facade {
    private SubSystemA obj1 = new SubSystemA();
    private SubSystemB obj2 = new SubSystemB();
    private SubSystemC obj3 = new SubSystemC();

    public void method() {
        obj1.MethodA();
        obj2.MethodB();
        obj3.MethodC();
    }
}
```

由于在外观类中维持了对子系统对象的引用，客户端可以通过外观类来间接调用子系统对象的业务方法，而无须与子系统对象直接交互。引入外观类后，客户端代码变得非常简单:

```java
public class Program {
    public static void main(String[] args) {
        Facade facade = new Facade();
        facade.method();
    }
}
```

# 优缺点

## 优点

- 它对客户端屏蔽了子系统组件，减少了客户端所需处理的对象数目，并使得子系统使用起来更加容易。通过引入外观模式，客户端代码将变得很简单，与之关联的对象也很少。
- 它实现了子系统与客户端之间的松耦合关系，这使得子系统的变化不会影响到调用它的客户端，只需要调整外观类即可。
- 一个子系统的修改对其他子系统没有任何影响，而且子系统内部变化也不会影响到外观对象。

## 缺点

- 不能很好地限制客户端直接使用子系统类，如果对客户端访问子系统类做太多的限制则减少了可变性和灵活性。
- 如果设计不当，增加新的子系统可能需要修改外观类的源代码，违背了开闭原则。