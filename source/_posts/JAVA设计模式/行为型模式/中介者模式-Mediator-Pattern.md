---
title: 中介者模式-Mediator Pattern
date: 2019-01-18 11:23:25
tags:
  - java
  - 设计模式
categories: 
  - JAVA设计模式
---

# 概述
> 用一个中介对象（中介者）来封装一系列的对象交互，中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。中介者模式又称为调停者模式，它是一种对象行为型模式。

<!-- more -->

# 组成

- Mediator（抽象中介者）
> 它定义一个接口，该接口用于与各同事对象之间进行通信。
- ConcreteMediator（具体中介者）
> 它是抽象中介者的子类，通过协调各个同事对象来实现协作行为，它维持了对各个同事对象的引用。
- Colleague（抽象同事类）
> 它定义各个同事类公有的方法，并声明了一些抽象方法来供子类实现，同时它维持了一个对抽象中介者类的引用，其子类可以通过该引用来与中介者通信。
- ConcreteColleague（具体同事类）
> 它是抽象同事类的子类；每一个同事对象在需要和其他同事对象通信时，先与中介者通信，通过中介者来间接完成与其他同事类的通信；在具体同事类中实现了在抽象同事类中声明的抽象方法。

# 适用场景

- 对象之间存在复杂的引用关系
- 对象难以复用
- 通过一个中间类来封装多个类中的行为，而又不想生成太多的子类

# 案例

## UML类图

![](https://i.imgur.com/6GT27VJ.png)

Component充当抽象同事类，Button、List、ComboBox和TextBox充当具体同事类，Mediator充当抽象中介者类，ConcreteMediator充当具体中介者类，ConcreteMediator维持了对具体同事类的引用，为了简化ConcreteMediator类的代码，我们在其中只定义了一个Button对象和一个TextBox对象。

Component:

```java
//抽象同事类
public abstract class Component {
    @Setter
    protected Mediator mediator;

    //转发调用
    public void changed() {
        mediator.componentChanged(this);
    }

    public abstract void update();
}
//按钮类(具体同事类)
public class Button extends Component {
    @Override
    public void update() {
        //按钮不产生交互
    }
}
//列表框类(具体同事类)
public class List extends Component {
    @Override
    public void update() {
        System.out.println("列表框增加一项：张无忌。");
    }

    public void select() {
        System.out.println("列表框选中项：小龙女。");
    }
}
//组合框类(具体同事类)
public class ComboBox extends Component {
    @Override
    public void update() {
        System.out.println("组合框增加一项：张无忌。");
    }

    public void select() {
        System.out.println("组合框选中项：小龙女。");
    }

}
//文本框类(具体同事类)
public class TextBox extends Component {
    @Override
    public void update() {
        System.out.println("客户信息增加成功后文本框清空。");
    }

    public void setText() {
        System.out.println("文本框显示：小龙女。");
    }

}
//抽象中介者
public abstract class Mediator {
    public abstract void componentChanged(Component c);
}
//具体中介者
public class ConcreteMediator extends Mediator {

    //维持对各个同事对象的引用
    public Button addButton;
    public List list;
    public TextBox userNameTextBox;
    public ComboBox cb;

    //封装同事对象之间的交互
    @Override
    public void componentChanged(Component c) {
        //单击按钮
        if (c == addButton) {
            System.out.println("--单击增加按钮--");
            list.update();
            cb.update();
            userNameTextBox.update();
        }
        //从列表框选择客户
        else if (c == list) {
            System.out.println("--从列表框选择客户--");
            cb.select();
            userNameTextBox.setText();
        }
        //从组合框选择客户
        else if (c == cb) {
            System.out.println("--从组合框选择客户--");
            cb.select();
            userNameTextBox.setText();
        }
    }
}
```

客户端:

```java
public class Client {
    public static void main(String args[]) {
        //定义中介者对象
        ConcreteMediator mediator;
        mediator = new ConcreteMediator();

        //定义同事对象
        Button addBT = new Button();
        List list = new List();
        ComboBox cb = new ComboBox();
        TextBox userNameTB = new TextBox();

        addBT.setMediator(mediator);
        list.setMediator(mediator);
        cb.setMediator(mediator);
        userNameTB.setMediator(mediator);

        mediator.addButton = addBT;
        mediator.list = list;
        mediator.cb = cb;
        mediator.userNameTextBox = userNameTB;

        addBT.changed();
        System.out.println("-----------------------------");
        list.changed();
    }
}
```

输出结果:

```
--单击增加按钮--
列表框增加一项：张无忌。
组合框增加一项：张无忌。
客户信息增加成功后文本框清空。
-----------------------------
--从列表框选择客户--
组合框选中项：小龙女。
文本框显示：小龙女。
```

# 优缺点

## 优点

- 简化了对象之间的交互
> 它用中介者和同事的一对多交互代替了原来同事之间的多对多交互，一对多关系更容易理解、维护和扩展，将原本难以理解的网状结构转换成相对简单的星型结构。
- 可将各同事对象解耦
> 中介者有利于各同事之间的松耦合，我们可以独立的改变和复用每一个同事和中介者，增加新的中介者和新的同事类都比较方便，更好地符合“开闭原则”。
- 减少子类生成
> 中介者将原本分布于多个对象间的行为集中在一起，改变这些行为只需生成新的中介者子类即可，这使各个同事类可被重用，无须对同事类进行扩展。

## 缺点

- 可能会导致具体中介者类非常复杂
> 在具体中介者类中包含了大量同事之间的交互细节，可能会导致具体中介者类非常复杂，使得系统难以维护。

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/mediatorPattern)
👉 [返回设计模式概览](#JAVA设计模式/设计模式概览)