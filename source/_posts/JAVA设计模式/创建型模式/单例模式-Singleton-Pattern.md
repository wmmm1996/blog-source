---
title: 单例模式-Singleton Pattern
date: 2019-01-15 10:25:04
tags:
  - java
  - 设计模式
categories: 
  - JAVA设计模式
---

# 概述
> 单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例，这个类称为单例类，它提供全局访问的方法。

<!-- more -->

# 组成

- Singleton：单例

# 适用场景

- 系统只需要一个实例对象，如系统要求提供一个唯一的序列号生成器，或者需要考虑资源消耗太大而只允许创建一个对象。
- 客户调用类的单个实例只允许使用一个公共访问点，除了该公共访问点，不能通过其他途径访问该实例。
- 在一个系统中要求一个类只有一个实例时才应当使用单例模式。反过来，如果一个类可以有几个实例共存，就需要对单例模式进行改进，使之成为多例模式。

# 案例

UML类图

![](https://i.loli.net/2019/01/14/5c3ca9c87c39b.png)

单例

```java
public class Singleton {
    private static Singleton instance;

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }

    public void show() {
        System.out.println("show");
    }
    private Singleton() {}

}
```

测试类

```java
public class Test {
    public static void main(String[] args) {
        Singleton.getInstance().show();
    }
}
```

输出结果

```
show
```

# 优缺点

## 优点

- 提供了对唯一实例的受控访问。
> 因为单例类封装了它的唯一实例，所以它可以严格控制客户怎样以及何时访问它，并为设计及开发团队提供了共享的概念。
- 节约系统资源
> 由于在系统内存中只存在一个对象，因此可以节约系统资源，对于一些需要频繁创建和销毁的对象，单例模式无疑可以提高系统的性能。
- 允许可变数目的实例。
> 我们可以基于单例模式进行扩展，使用与单例控制相似的方法来获得指定个数的对象实例。

## 缺点

- 由于单例模式中没有抽象层，因此单例类的扩展有很大的困难。
- 单例类的职责过重，在一定程度上违背了“单一职责原则”。
> 因为单例类既充当了工厂角色，提供了工厂方法，同时又充当了产品角色，包含一些业务方法，将产品的创建和产品的本身的功能融合到一起。

# 扩展

上面的是懒汉单例模式

## 饿汉单例模式

饿汉式单例是指在方法调用前，实例就已经创建好了。

```java
public class MySingleton {
	
	private static MySingleton instance = new MySingleton();
	
	private MySingleton(){}
	
	public static MySingleton getInstance() {
		return instance;
	}
}
```

## 单例模式的线程安全问题

### 使用synchronized关键字(效率低下)

对singleton对象使用volatile关键字进行限制，保证其对所有线程的可见性，并且禁止对其进行指令重排序优化。

```java
public class Singleton1 {
    private static volatile Singleton1 instance = null;

    private Singleton1() {
    }

    public static Singleton1 getInstance() {
        synchronized (Singleton1.class) {
            if (instance == null) {
                instance = new Singleton1();
            }
            return instance;
        }
    }
}
```

### 兼顾线程安全和效率(推荐)

```java
public class Singleton1 {
    private static volatile Singleton1 singleton = null;

    private Singleton1() {
    }

    public static Singleton1 getSingleton() {
        if (singleton == null) {
            synchronized (Singleton.class) {
                if (singleton == null) {
                    singleton = new Singleton1();
                }
            }
        }
        return singleton;
    }
}
```

### 静态内部类法

把Singleton实例放到一个静态内部类中，这样就避免了静态实例在Singleton类加载的时候就创建对象，并且由于静态内部类只会被加载一次，所以这种写法也是线程安全的。

```java
public class Singleton {
    private static class SingletonInner {
        private static Singleton singleton = new Singleton();
    }

    private Singleton() {
    }

    public static Singleton getSingleton() {
        return SingletonInner.singleton;
    }

}
```

### 枚举实现单例模式

使用枚举除了线程安全和防止反射强行调用构造器之外，还提供了自动序列化机制，防止反序列化的时候创建新的对象。因此，Effective Java推荐尽可能地使用枚举来实现单例。

```JAVA
public enum Singleton {
    INSTANCE;
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/singletonPattern)
👉 [返回设计模式概览](#JAVA设计模式/设计模式概览)