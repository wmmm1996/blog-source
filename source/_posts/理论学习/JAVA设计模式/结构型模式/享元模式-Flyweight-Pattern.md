---
title: 享元模式-Flyweight Pattern
date: 2019-01-16 10:09:59
tags:
  - java
  - 设计模式
categories: 
  - 理论学习
  - JAVA设计模式
---

# 概述
> 运用共享技术有效地支持大量细粒度对象的复用。系统只使用少量的对象，而这些对象都很相似，状态变化很小，可以实现对象的多次复用。由于享元模式要求能够共享的对象必须是细粒度对象，因此它又称为轻量级模式。

<!-- more -->

# 组成

- Flyweight（抽象享元类）
> 通常是一个接口或抽象类，在抽象享元类中声明了具体享元类公共的方法，这些方法可以向外界提供享元对象的内部数据（内部状态），同时也可以通过这些方法来设置外部数据（外部状态）。
- ConcreteFlyweight（具体享元类）
> 它实现了抽象享元类，其实例称为享元对象；在具体享元类中为内部状态提供了存储空间。通常我们可以结合单例模式来设计具体享元类，为每一个具体享元类提供唯一的享元对象。
- UnsharedConcreteFlyweight（非共享具体享元类）
> 并不是所有的抽象享元类的子类都需要被共享，不能被共享的子类可设计为非共享具体享元类；当需要一个非共享具体享元类的对象时可以直接通过实例化创建。
- FlyweightFactory（享元工厂类）
> 提供一个用于存储享元对象的享元池，当用户需要对象时，首先从享元池中获取，如果享元池中不存在，则创建一个新的享元对象返回给用户，并在享元池中保存该新增对象。

# 适用场景

- 一个系统有大量相同或者相似的对象，造成内存的大量耗费。
- 对象的大部分状态都可以外部化，可以将这些外部状态传入对象中。
- 在使用享元模式时需要维护一个存储享元对象的享元池，而这需要耗费一定的系统资源，因此，应当在需要多次重复使用享元对象时才值得使用享元模式。

# 案例

UML类图

![](https://i.imgur.com/r4sZZoJ.png)

```java
//围棋棋子类(抽象享元类)
public abstract class IgoChessman {
    public abstract String getColor();

    public void display() {
        System.out.println("棋子颜色：" + this.getColor());
    }
}
//黑色棋子类
public class BlackIgoChessman extends IgoChessman {
    @Override
    public String getColor() {
        return "黑色";
    }
}
//白色棋子类
public class WhiteIgoChessman extends IgoChessman {
    @Override
    public String getColor() {
        return "白色";
    }
}
//围棋棋子工厂类(享元工厂类)
public class IgoChessmanFactory {
    private static Hashtable<String, IgoChessman> ht; //使用Hashtable来存储享元对象，充当享元池

    private IgoChessmanFactory() {
    }

    static {
        ht = new Hashtable<>();
        IgoChessman black, white;
        black = new BlackIgoChessman();
        ht.put("b", black);
        white = new WhiteIgoChessman();
        ht.put("w", white);
    }

    //通过key来获取存储在Hashtable中的享元对象
    public static IgoChessman getIgoChessman(String color) {
        return (IgoChessman) ht.get(color);
    }
}
```

客户端类:

```java
public class Client {
    public static void main(String[] args) {
        IgoChessman black1, black2, black3, white1, white2;
        //通过享元工厂获取三颗黑子
        black1 = IgoChessmanFactory.getIgoChessman("b");
        black2 = IgoChessmanFactory.getIgoChessman("b");
        black3 = IgoChessmanFactory.getIgoChessman("b");
        System.out.println("判断两颗黑子是否相同：" + (black1 == black2));

        //通过享元工厂获取两颗白子
        white1 = IgoChessmanFactory.getIgoChessman("w");
        white2 = IgoChessmanFactory.getIgoChessman("w");
        System.out.println("判断两颗白子是否相同：" + (white1 == white2));

        //显示棋子
        black1.display();
        black2.display();
        black3.display();
        white1.display();
        white2.display();
    }
}
```

输出结果:

```
判断两颗黑子是否相同：true
判断两颗白子是否相同：true
棋子颜色：黑色
棋子颜色：黑色
棋子颜色：黑色
棋子颜色：白色
棋子颜色：白色
```

从输出结果可以看出，虽然我们获取了三个黑子对象和两个白子对象，但是它们的内存地址相同，也就是说，它们实际上是同一个对象。

# 优缺点

## 优点

- 节约系统资源，提高系统性能
> 可以极大减少内存中对象的数量,使得相同或相似对象在内存中只保存一份，从而可以节约系统资源，提高系统性能。
- 享元对象可以在不同的环境中被共享
> 享元模式的外部状态相对独立，而且不会影响其内部状态，从而使得享元对象可以在不同的环境中被共享。

## 缺点

- 系统变得复杂
> 享元模式使得系统变得复杂，需要分离出内部状态和外部状态，这使得程序的逻辑复杂化。
- 运行时间变长
> 为了使对象可以共享，享元模式需要将享元对象的部分状态外部化，而读取外部状态将使得运行时间变长。

# 练习
> Sunny软件公司欲开发一个多功能文档编辑器，在文本文档中可以插入图片、动画、视频等多媒体资料，为了节约系统资源，相同的图片、动画和视频在同一个文档中只需保存一份，但是可以多次重复出现，而且它们每次出现时位置和大小均可不同。试使用享元模式设计该文档编辑器。

```java
//抽象享元
@Data
public abstract class AbstractMediaData {
    protected int x;
    protected int y;

    public abstract String getName();

    public void display() {
        System.out.println("我是" + this.getName() + ",我的坐标 : " + x + "," + y);
    }
}
public class GIFMediaData extends AbstractMediaData {
    @Override
    public String getName() {
        return "动画";
    }
}
public class ImageMediaData extends AbstractMediaData {
    @Override
    public String getName() {
        return "图像";
    }
}
public class VideoMediaData extends AbstractMediaData {
    @Override
    public String getName() {
        return "视频";
    }
}
//享元工厂
public class MediaDataFactory {
    private static Hashtable<String, AbstractMediaData> ht; //使用Hashtable来存储享元对象，充当享元池

    private MediaDataFactory() {
    }

    static {
        ht = new Hashtable<>();
        ht.put("image", new ImageMediaData());
        ht.put("video", new VideoMediaData());
        ht.put("gif", new GIFMediaData());
    }

    public static AbstractMediaData get(String key) {
        return ht.get(key);
    }
}
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        AbstractMediaData gif = MediaDataFactory.get("gif");
        gif.setX(10);
        gif.setY(100);
        gif.display();
        AbstractMediaData gif1 = MediaDataFactory.get("gif");
        gif1.setX(5);
        gif1.setY(5);
        System.out.println("是否是同一个动画 : " + (gif == gif1));
        gif1.display();
    }
}
```

输出结果:

```
我是动画,我的坐标 : 10,100
是否是同一个动画 : true
我是动画,我的坐标 : 5,5
```

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/flyweightPattern)
👉 [返回设计模式概览](../../设计模式概览)