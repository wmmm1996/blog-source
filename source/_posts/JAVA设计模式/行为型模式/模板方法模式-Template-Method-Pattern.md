---
title: 模板方法模式-Template Method Pattern
date: 2019-01-19 20:39:22
tags:
  - java
  - 设计模式
categories: 
  - JAVA设计模式
---

# 概述
> 定义一个操作中算法的框架，而将一些步骤延迟到子类中。模板方法模式使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

<!-- more -->

# 组成

- AbstractClass（抽象类）
> 在抽象类中定义了一系列基本操作(PrimitiveOperations)，这些基本操作可以是具体的，也可以是抽象的，每一个基本操作对应算法的一个步骤，在其子类中可以重定义或实现这些步骤。同时，在抽象类中实现了一个模板方法(Template Method)，用于定义一个算法的框架，模板方法不仅可以调用在抽象类中实现的基本方法，也可以调用在抽象类的子类中实现的基本方法，还可以调用其他对象中的方法。
- ConcreteClass（具体子类）
> 它是抽象类的子类，用于实现在父类中声明的抽象基本操作以完成子类特定算法的步骤，也可以覆盖在父类中已经实现的具体基本操作。

# 适用场景

- 一次性实现一个算法的不变部分，并将可变的行为留给子类来实现。
- 各子类中公共的行为应被提取出来并集中到一个公共父类中以避免代码重复。

# 案例

某软件公司欲为某银行的业务支撑系统开发一个利息计算模块，利息计算流程如下：
1. 系统根据账号和密码验证用户信息，如果用户信息错误，系统显示出错提示；
2. 如果用户信息正确，则根据用户类型的不同使用不同的利息计算公式计算利息（如活期账户和定期账户具有不同的利息计算公式）；
3. 系统显示利息。

```java
//抽象类角色
public abstract class Account {
    //基本方法——具体方法
    public boolean validate(String account, String password) {
        System.out.println("账号：" + account);
        System.out.println("密码：" + password);
        //模拟登录
        return "张无忌".equals(account) && "123456".equals(password);
    }

    //基本方法——抽象方法
    public abstract void calculateInterest();

    //基本方法——具体方法
    public void display() {
        System.out.println("显示利息！");
    }

    //模板方法
    public final void handle(String account, String password) {
        if (!validate(account, password)) {
            System.out.println("账户或密码错误！");
            return;
        }
        calculateInterest();
        display();
    }

}
//活期账户类，充当具体子类
public class CurrentAccount extends Account {
    @Override
    public void calculateInterest() {
        System.out.println("按活期利率计算利息！");
    }
}
//定期账户类，充当具体子类
public class SavingAccount extends Account {
    @Override
    public void calculateInterest() {
        System.out.println("按定期利率计算利息！");
    }
}
```

客户端：

```java
public class Client {
    public static void main(String[] args) {
        Account account = new CurrentAccount();
        account.handle("张无忌","123456");
    }
}
```

输出结果:

```
账号：张无忌
密码：123456
按活期利率计算利息！
显示利息！
```

# 优缺点

## 优点

- 在父类中形式化地定义一个算法，而由它的子类来实现细节的处理，在子类实现详细的处理算法时并不会改变算法中步骤的执行次序。
- 模板方法模式是一种代码复用技术，它在类库设计中尤为重要，它提取了类库中的公共行为，将公共行为放在父类中，而通过其子类来实现不同的行为，它鼓励我们恰当使用继承来实现代码复用。
- 在模板方法模式中可以通过子类来覆盖父类的基本方法，不同的子类可以提供基本方法的不同实现，更换和增加新的子类很方便，符合单一职责原则和开闭原则。

## 缺点

- 需要为每一个基本方法的不同实现提供一个子类，如果父类中可变的基本方法太多，将会导致类的个数增加，系统更加庞大，设计也更加抽象，此时，可结合桥接模式来进行设计。

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/templateMethodPattern)
👉 [返回设计模式概览](../../设计模式概览)