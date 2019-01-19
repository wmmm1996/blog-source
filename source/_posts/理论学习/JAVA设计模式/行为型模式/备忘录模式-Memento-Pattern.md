---
title: 备忘录模式-Memento Pattern
date: 2019-01-18 12:54:31
tags:
  - JAVA设计模式
categories: 
  - 理论学习
  - JAVA设计模式
---

# 概述
> 在不破坏封装的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样可以在以后将对象恢复到原先保存的状态。它是一种对象行为型模式，其别名为Token。

# 组成

- Originator（原发器）
> 它是一个普通类，可以创建一个备忘录，并存储它的当前内部状态，也可以使用备忘录来恢复其内部状态，一般将需要保存内部状态的类设计为原发器。
- Memento（备忘录)
> 存储原发器的内部状态，根据原发器来决定保存哪些内部状态。
- Caretaker（负责人）
> 负责人又称为管理者，它负责保存备忘录，但是不能对备忘录的内容进行操作或检查。在负责人类中可以存储一个或多个备忘录对象，它只负责存储对象，而不能修改对象，也无须知道对象的实现细节。

# 适用场景

- 保存一个对象在某一个时刻的全部状态或部分状态，这样以后需要时它能够恢复到先前的状态，实现撤销操作。
- 防止外界对象破坏一个对象历史状态的封装性，避免将对象历史状态的实现细节暴露给外界对象。

# 案例

## UML类图

![](https://i.imgur.com/gDne3e5.png)

Chessman充当原发器，ChessmanMemento充当备忘录，MementoCaretaker充当负责人，在MementoCaretaker中定义了一个ChessmanMemento类型的对象，用于存储备忘录。

Chessman:

```java
//象棋棋子类：原发器
@Data
@AllArgsConstructor
public class Chessman {
    private String label;
    private int x;
    private int y;

    //保存状态
    public ChessmanMemento save() {
        return new ChessmanMemento(this.label, this.x, this.y);
    }

    //恢复状态
    public void restore(ChessmanMemento memento) {
        this.label = memento.getLabel();
        this.x = memento.getX();
        this.y = memento.getY();
    }
}
```

ChessmanMemento:

```java
//象棋棋子备忘录类：备忘录
@Data
@AllArgsConstructor
public class ChessmanMemento {
    private String label;
    private int x;
    private int y;
}
```

MementoCaretaker:

```java
//象棋棋子备忘录管理类：负责人
@Data
public class MementoCaretaker {
    private ChessmanMemento memento;
}
```

客户端:

```java
public class Client {
    public static void main(String args[]) {
        MementoCaretaker mc = new MementoCaretaker();
        Chessman chess = new Chessman("车", 1, 1);
        display(chess);
        mc.setMemento(chess.save()); //保存状态
        chess.setY(4);
        display(chess);
        mc.setMemento(chess.save()); //保存状态
        display(chess);
        chess.setX(5);
        display(chess);
        System.out.println("******悔棋******");
        chess.restore(mc.getMemento()); //恢复状态
        display(chess);
    }

    public static void display(Chessman chess) {
        System.out.println("棋子" + chess.getLabel() + "当前位置为：" + "第" + chess.getX() + "行" + "第" + chess.getY() + "列。");
    }
}
```

输出结果:

```
棋子车当前位置为：第1行第1列。
棋子车当前位置为：第1行第4列。
棋子车当前位置为：第1行第4列。
棋子车当前位置为：第5行第4列。
******悔棋******
棋子车当前位置为：第1行第4列。
```

# 优缺点

## 优点

- 可实现状态恢复
> 它提供了一种状态恢复的实现机制，使得用户可以方便地回到一个特定的历史步骤，当新的状态无效或者存在问题时，可以使用暂时存储起来的备忘录将状态复原。
- 实现了对信息的封装
> 备忘录实现了对信息的封装，一个备忘录对象是一种原发器对象状态的表示，不会被其他代码所改动。备忘录保存了原发器的状态，采用列表、堆栈等集合来存储备忘录对象可以实现多次撤销操作。

## 缺点

- 资源消耗过大
> 如果需要保存的原发器类的成员变量太多，就不可避免需要占用大量的存储空间，每保存一次对象的状态都需要消耗一定的系统资源。

[返回设计模式概览](#理论学习/JAVA设计模式/设计模式概览)