---
title: 迭代器模式-Iterator Pattern
date: 2019-01-18 10:51:13
tags:
  - java
  - 设计模式
categories: 
  - 理论学习
  - JAVA
---

# 概述

提供一种方法来访问聚合对象，而不用暴露这个对象的内部表示，其别名为游标(Cursor)。<!-- more -->

# 组成

- **Iterator（抽象迭代器）**：它定义了访问和遍历元素的接口，声明了用于遍历数据元素的方法，例如：用于获取第一个元素的first()方法，用于访问下一个元素的next()方法，用于判断是否还有下一个元素的hasNext()方法，用于获取当前元素的currentItem()方法等，在具体迭代器中将实现这些方法。
- **ConcreteIterator（具体迭代器）**：它实现了抽象迭代器接口，完成对聚合对象的遍历，同时在具体迭代器中通过游标来记录在聚合对象中所处的当前位置，在具体实现时，游标通常是一个表示位置的非负整数。
- **Aggregate（抽象聚合类）**：它用于存储和管理元素对象，声明一个createIterator()方法用于创建一个迭代器对象，充当抽象迭代器工厂角色。
- **ConcreteAggregate（具体聚合类）**：它实现了在抽象聚合类中声明的createIterator()方法，该方法返回一个与该具体聚合类对应的具体迭代器ConcreteIterator实例。

# 适用场景

- 访问一个聚合对象的内容而无须暴露它的内部表示。将聚合对象的访问与内部数据的存储分离，使得访问聚合对象时无须了解其内部实现细节。
- 需要为一个聚合对象提供多种遍历方式。
- 为遍历不同的聚合结构提供一个统一的接口，在该接口的实现类中为不同的聚合结构提供不同的遍历方式，而客户端可以一致性地操作该接口。

# 案例

## UML类图

![](https://i.imgur.com/77IX6sO.png)

AbstractObjectList充当抽象聚合类，ProductList充当具体聚合类，AbstractIterator充当抽象迭代器，ProductIterator充当具体迭代器。

AbstractObjectList:

```java
public abstract class AbstractObjectList {
    protected List<Object> objects = new ArrayList<Object>();

    public AbstractObjectList(List objects) {
        this.objects = objects;
    }

    public void addObject(Object obj) {
        this.objects.add(obj);
    }

    public void removeObject(Object obj) {
        this.objects.remove(obj);
    }

    public List getObjects() {
        return this.objects;
    }

    //声明创建迭代器对象的抽象工厂方法
    public abstract AbstractIterator createIterator();
}
```
ProductList:

```java
public class ProductList extends AbstractObjectList {
    public ProductList(List products) {
        super(products);
    }

    //实现创建迭代器对象的具体工厂方法
    @Override
    public AbstractIterator createIterator() {
        return new ProductIterator(this);
    }
}
```

AbstractIterator:

```java
public interface AbstractIterator {
    void next(); //移至下一个元素

    boolean isLast(); //判断是否为最后一个元素

    void previous(); //移至上一个元素

    boolean isFirst(); //判断是否为第一个元素

    Object getNextItem(); //获取下一个元素

    Object getPreviousItem(); //获取上一个元素
}
```

ProductIterator:

```java
public class ProductIterator implements AbstractIterator {
    private ProductList productList;
    private List products;
    private int cursor1; //定义一个游标，用于记录正向遍历的位置
    private int cursor2; //定义一个游标，用于记录逆向遍历的位置

    public ProductIterator(ProductList list) {
        this.productList = list;
        this.products = list.getObjects(); //获取集合对象
        cursor1 = 0; //设置正向遍历游标的初始值
        cursor2 = products.size() - 1; //设置逆向遍历游标的初始值
    }

    @Override
    public void next() {
        if (cursor1 < products.size()) {
            cursor1++;
        }
    }

    @Override
    public boolean isLast() {
        return (cursor1 == products.size());
    }

    @Override
    public void previous() {
        if (cursor2 > -1) {
            cursor2--;
        }
    }

    @Override
    public boolean isFirst() {
        return (cursor2 == -1);
    }

    @Override
    public Object getNextItem() {
        return products.get(cursor1);
    }

    @Override
    public Object getPreviousItem() {
        return products.get(cursor2);
    }
}
```

客户端:

```java
public class Client {
    public static void main(String args[]) {
        ArrayList products = new ArrayList();
        products.add("倚天剑");
        products.add("屠龙刀");
        products.add("断肠草");
        products.add("葵花宝典");
        products.add("四十二章经");

        AbstractObjectList list;
        AbstractIterator iterator;

        list = new ProductList(products); //创建聚合对象
        iterator = list.createIterator();   //创建迭代器对象

        System.out.println("正向遍历：");
        while (!iterator.isLast()) {
            System.out.print(iterator.getNextItem() + "，");
            iterator.next();
        }
        System.out.println();
        System.out.println("-----------------------------");
        System.out.println("逆向遍历：");
        while (!iterator.isFirst()) {
            System.out.print(iterator.getPreviousItem() + "，");
            iterator.previous();
        }
    }
}
```

输出结果:

```
正向遍历：
倚天剑，屠龙刀，断肠草，葵花宝典，四十二章经，
-----------------------------
逆向遍历：
四十二章经，葵花宝典，断肠草，屠龙刀，倚天剑，
```

- **增加一个新的具体聚合类**:只需增加一个新的聚合子类和一个新的具体迭代器类即可，原有类库代码无须修改，符合“开闭原则”
- **需要为ProductList类更换一个迭代器**:只需要增加一个新的具体迭代器类作为抽象迭代器类的子类，重新实现遍历方法，原有迭代器代码无须修改，也符合“开闭原则”
- **在迭代器中增加新的方法**:需要修改抽象迭代器源代码，这将违背“开闭原则”

# 优缺点

## 优点

- 它支持以不同的方式遍历一个聚合对象，在同一个聚合对象上可以定义多种遍历方式。
- 迭代器简化了聚合类。由于引入了迭代器，在原有的聚合对象中不需要再自行提供数据遍历等方法，这样可以简化聚合类的设计。
- 在迭代器模式中，由于引入了抽象层，增加新的聚合类和迭代器类都很方便，无须修改原有代码，满足“开闭原则”的要求。

## 缺点

- 由于迭代器模式将存储数据和遍历数据的职责分离，增加新的聚合类需要对应增加新的迭代器类，类的个数成对增加，这在一定程度上增加了系统的复杂性。
- 抽象迭代器的设计难度较大，需要充分考虑到系统将来的扩展，例如JDK内置迭代器Iterator就无法实现逆向遍历，如果需要实现逆向遍历，只能通过其子类ListIterator等来实现，而ListIterator迭代器无法用于操作Set类型的聚合对象。在自定义迭代器时，创建一个考虑全面的抽象迭代器并不是件很容易的事情。