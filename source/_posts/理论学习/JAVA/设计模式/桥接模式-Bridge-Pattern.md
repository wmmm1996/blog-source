---
title: 桥接模式-Bridge Pattern
date: 2019-01-15 18:16:08
tags:
  - java
  - 设计模式
categories: 
  - 理论学习
  - JAVA
  - 设计模式
---

# 概述

桥接模式基于类的最小设计原则，通过使用封装，聚合以及继承等行为来让不同的类承担不同的责任。它的主要特点是把抽象（abstraction）与行为实现（implementation）分离开来，从而可以保持各部分的独立性以及应对它们的功能扩展。<!-- more -->

# 适用场景

处理多层继承结构，多维度变化的场景。将各个维度设计成独立的继承结构，使得各个维度可以独立的扩展。

# 案例

以电脑大卖场为例，电脑商品可简单用下图表示:

![](https://i.loli.net/2019/01/15/5c3dbf7e7735c.png)

图中将电脑结构分成 3 级，第 1 级就是商品名称（电脑），第 2 级是电脑类型分类，第 3 级是品牌分类。
我们从 2 个维度分类，将它们分离独立出来，即将电脑类型和电脑品牌分离出来。

UML类图

![](https://i.loli.net/2019/01/15/5c3dbf7e5d6bb.png)

品牌维度：

```java
public interface Brand {
    void info();
}
class LenovoBrand implements Brand {
    @Override
    public void info() {
        System.out.println("联想");
    }
    
}
class AsusBrand implements Brand {
    
    @Override
    public void info() {
        System.out.println("华硕");
    }
    
}
```

类型维度：

```java
public abstract class Computer {
    protected Brand brand;
    public Computer(Brand brand) {
        this.brand = brand;
    }
    
    public void info() {
        this.brand.info();
    }
}
class Desktop extends Computer {
    public Desktop(Brand brand) {
        super(brand);
    }
    
    public void info() {
        super.info();
        System.out.println("台式电脑");
    }
    
}
class Laptop extends Computer {
    public Laptop(Brand brand) {
        super(brand);
    }
    
    public void info() {
        super.info();
        System.out.println("笔记本电脑");
    }
    
}
```

客户端：

```java
public class Client {
    public static void main(String[] args) {
        Computer computer = new Desktop(new LenovoBrand());
        computer.info();
    }
}
```

结果输出:

```
联想
台式电脑
```

# 反例

传统的继承方法实现上面的电脑案例有什么缺点?

```java
public interface Computer {
    void info();
}
class Desktop implements Computer {
    @Override
    public void info() {
        System.out.println("台式电脑！");
    }
}
class Laptop implements Computer {
    @Override
    public void info() {
        System.out.println("笔记本电脑！");
    }
}
class LenovoDesktop extends Desktop {
    @Override
    public void info() {
        System.out.println("联想台式电脑");
    }
}
class AsusDesktop extends Desktop {
    @Override
    public void info() {
        System.out.println("华硕台式电脑");
    }
}
class LenovoLaptop extends Laptop {
    @Override
    public void info() {
        System.out.println("联想笔记本电脑");
    }
}
class AsusLaptop extends Laptop {
    @Override
    public void info() {
        System.out.println("华硕笔记本电脑");
    }
}
```

## UML类图

![](https://i.loli.net/2019/01/15/5c3dbf7e51939.png)

## 缺点

- 扩展性问题：如果新增平板电脑分类，需要添加 N 个品牌子类。如果新增索尼品牌，其他电脑分类也需要添加相应子类。
- 违背单一职责原则：新增类型或新增品牌都会导致第 3 级目录结构的变化。



