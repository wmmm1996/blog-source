---
title: 职责链模式-Chain of Responsibility Pattern
date: 2019-01-16 14:20:57
tags:
  - java
  - 设计模式
categories: 
  - 理论学习
  - JAVA设计模式
---

# 概述
> 避免请求发送者与接收者耦合在一起，让多个对象都有可能接收请求，将这些对象连接成一条链，并且沿着这条链传递请求，直到有对象处理它为止。

<!-- more -->

# 组成

- Handler（抽象处理者）
> 它定义了一个处理请求的接口，一般设计为抽象类，由于不同的具体处理者处理请求的方式不同，因此在其中定义了抽象请求处理方法。
- ConcreteHandler（具体处理者）
> 它是抽象处理者的子类，可以处理用户请求，在具体处理者类中实现了抽象处理者中定义的抽象请求处理方法，在处理请求之前需要进行判断，看是否有相应的处理权限，如果可以处理请求就处理它，否则将请求转发给后继者；在具体处理者中可以访问链中下一个对象，以便请求的转发。

# 适用场景

- 有多个对象可以处理同一个请求，具体哪个对象处理该请求待运行时刻再确定，客户端只需将请求提交到链上，而无须关心请求的处理对象是谁以及它是如何处理的。
- 在不明确指定接收者的情况下，向多个对象中的一个提交一个请求。
- 可动态指定一组对象处理请求，客户端可以动态创建职责链来处理请求，还可以改变链中处理者之间的先后次序。

# 案例

UML类图

![](https://i.imgur.com/jFyqrYW.png)

```java
@AllArgsConstructor
@Data
//请求类
public class PurchaseRequest {
    private double amount;  //采购金额
    private int number;  //采购单编号
    private String purpose;  //采购目的
}
//抽象处理者（抽象传递者）
public abstract class Approver {
    @Setter
    protected Approver successor; //定义下一个处理者
    protected String name; //审批者姓名

    public Approver(String name) {
        this.name = name;
    }

    //抽象请求处理方法
    public abstract void processRequest(PurchaseRequest request);
}
//主任
public class Director extends Approver {
    public Director(String name) {
        super(name);
    }

    @Override
    public void processRequest(PurchaseRequest request) {
        if (request.getAmount() < 50000) {
            System.out.println("主任" + this.name + "审批采购单：" + request.getNumber() + "，金额：" + request.getAmount() + "元，采购目的：" + request.getPurpose() + "。");  //处理请求
        } else {
            this.successor.processRequest(request);  //转发请求
        }
    }
}
//副董事长
public class VicePresident extends Approver {
    public VicePresident(String name) {
        super(name);
    }

    @Override
    public void processRequest(PurchaseRequest request) {
        if (request.getAmount() < 100000) {
            System.out.println("副董事长" + this.name + "审批采购单：" + request.getNumber() + "，金额：" + request.getAmount() + "元，采购目的：" + request.getPurpose() + "。");  //处理请求
        } else {
            this.successor.processRequest(request);  //转发请求
        }
    }
}
//董事长
public class President extends Approver {
    public President(String name) {
        super(name);
    }

    @Override
    public void processRequest(PurchaseRequest request) {
        if (request.getAmount() < 500000) {
            System.out.println("董事长" + this.name + "审批采购单：" + request.getNumber() + "，金额：" + request.getAmount() + "元，采购目的：" + request.getPurpose() + "。");  //处理请求
        } else {
            this.successor.processRequest(request);  //转发请求
        }
    }
}
//董事会
public class Congress extends Approver {
    public Congress(String name) {
        super(name);
    }

    //具体请求处理方法
    @Override
    public void processRequest(PurchaseRequest request) {
        System.out.println("召开董事会审批采购单：" + request.getNumber() + "，金额：" + request.getAmount() + "元，采购目的：" + request.getPurpose() + "。");        //处理请求
    }
}
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        Approver wjzhang = new Director("张无忌");
        Approver gyang = new VicePresident("杨过");
        Approver jguo = new President("郭靖");
        Approver meeting = new Congress("董事会");

        //创建职责链
        wjzhang.setSuccessor(gyang);
        gyang.setSuccessor(jguo);
        jguo.setSuccessor(meeting);

        //创建采购单
        PurchaseRequest pr1 = new PurchaseRequest(45000, 10001, "购买倚天剑");
        wjzhang.processRequest(pr1);

        PurchaseRequest pr2 = new PurchaseRequest(60000, 10002, "购买《葵花宝典》");
        wjzhang.processRequest(pr2);

        PurchaseRequest pr3 = new PurchaseRequest(160000, 10003, "购买《金刚经》");
        wjzhang.processRequest(pr3);

        PurchaseRequest pr4 = new PurchaseRequest(800000, 10004, "购买桃花岛");
        wjzhang.processRequest(pr4);
    }
}
```

输出结果:

```
主任张无忌审批采购单：10001，金额：45000.0元，采购目的：购买倚天剑。
副董事长杨过审批采购单：10002，金额：60000.0元，采购目的：购买《葵花宝典》。
董事长郭靖审批采购单：10003，金额：160000.0元，采购目的：购买《金刚经》。
召开董事会审批采购单：10004，金额：800000.0元，采购目的：购买桃花岛。
```

由于链的创建过程由客户端负责，因此增加新的具体处理者类对原有类库无任何影响，无须修改已有类的源代码，符合"开闭原则"。

# 优缺点

## 优点

- 降低了系统的耦合度
> 职责链模式使得一个对象无须知道是其他哪一个对象处理其请求，对象仅需知道该请求会被处理即可，接收者和发送者都没有对方的明确信息，且链中的对象不需要知道链的结构，由客户端负责链的创建，降低了系统的耦合度。
- 简化对象的相互连接
> 请求处理对象仅需维持一个指向其后继者的引用，而不需要维持它对所有的候选处理者的引用，可简化对象的相互连接。
- 更多的灵活性
> 在给对象分派职责时，职责链可以给我们更多的灵活性，可以通过在运行时对该链进行动态的增加或修改来增加或改变处理一个请求的职责。
- 符合开闭原则
> 在系统中增加一个新的具体请求处理者时无须修改原有系统的代码，只需要在客户端重新建链即可，从这一点来看是符合开闭原则的。

## 缺点
- 能保证请求一定会被处理
> 由于一个请求没有明确的接收者，那么就不能保证它一定会被处理，该请求可能一直到链的末端都得不到处理；一个请求也可能因职责链没有被正确配置而得不到处理。
- 可能会影响系统性能,调试较困难
> 对于比较长的职责链，请求的处理可能涉及到多个处理对象，系统性能将受到一定影响，而且在进行代码调试时不太方便。
- 可能导致系统陷入死循环
> 如果建链不当，可能会造成循环调用，将导致系统陷入死循环。

# 练习
> Sunny软件公司的OA系统需要提供一个假条审批模块：如果员工请假天数小于3天，主任可以审批该假条；如果员工请假天数大于等于3天，小于10天，经理可以审批；如果员工请假天数大于等于10天，小于30天，总经理可以审批；如果超过30天，总经理也不能审批，提示相应的拒绝信息。试用职责链模式设计该假条审批模块。

## UML类图

![](https://i.imgur.com/hW1GIPR.png)

```java
//抽象责任链
@AllArgsConstructor
public abstract class AbstractHandler {
    protected AbstractHandler next; //责任链下一个
    public abstract void handle(Staff staff);
}
//具体责任链处理者
public class Director extends AbstractHandler {
    public Director(AbstractHandler next) {
        super(next);
    }

    @Override
    public void handle(Staff staff) {
        if (staff.getDay() < 3) {
            System.out.println("主任批了,请假天数 : " + staff.getDay());
        } else {
            this.next.handle(staff); //处理不了，给下一个责任人
        }
    }
}
public class Manager extends AbstractHandler {
    public Manager(AbstractHandler next) {
        super(next);
    }

    @Override
    public void handle(Staff staff) {
        if (staff.getDay() >= 3 && staff.getDay() < 10) {
            System.out.println("经理批了,请假天数 : " + staff.getDay());
        } else {
            this.next.handle(staff);
        }
    }
}
public class GeneralManager extends AbstractHandler {
    public GeneralManager(AbstractHandler next) {
        super(next);
    }

    @Override
    public void handle(Staff staff) {
        if (staff.getDay() >= 10 && staff.getDay() < 30) {
            System.out.println("总经理批了,请假天数 : " + staff.getDay());
        } else {
            System.out.println("天数太长了,批不了,请假天数 : " + staff.getDay());
        }
    }
}
//请假条(请求对象)
@Data
public class Staff {
    private String name; //员工姓名
    private int day; //请假天数
}
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        //创建责任链
        Director director = new Director(new Manager(new GeneralManager(null)));
        Staff staff = new Staff();
        staff.setName("小明");
        staff.setDay(24);
        Staff staff1 = new Staff();
        staff1.setName("小红");
        staff1.setDay(60);
        director.handle(staff);
        director.handle(staff1);
    }
}
```

输出结果:

```
总经理批了,请假天数 : 24
天数太长了,批不了,请假天数 : 60
```

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/chainOfResponsibilityPattern)
👉 [返回设计模式概览](../../设计模式概览)