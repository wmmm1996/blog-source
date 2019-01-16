---
title: 命令模式-Command Pattern
date: 2019-01-16 15:26:54
tags:
  - java
  - 设计模式
---

# 概述

将一个请求封装为一个对象，从而让我们可用不同的请求对客户进行参数化；对请求排队或者记录请求日志，以及支持可撤销的操作。<!-- more -->

# 组成

- **Command（抽象命令类）**：抽象命令类一般是一个抽象类或接口，在其中声明了用于执行请求的execute()等方法，通过这些方法可以调用请求接收者的相关操作。
- **ConcreteCommand（具体命令类）**：具体命令类是抽象命令类的子类，实现了在抽象命令类中声明的方法，它对应具体的接收者对象，将接收者对象的动作绑定其中。在实现execute()方法时，将调用接收者对象的相关操作(Action)。
- **Invoker（调用者）**：调用者即请求发送者，它通过命令对象来执行请求。一个调用者并不需要在设计时确定其接收者，因此它只与抽象命令类之间存在关联关系。在程序运行时可以将一个具体命令对象注入其中，再调用具体命令对象的execute()方法，从而实现间接调用请求接收者的相关操作。
- **Receiver（接收者）**：接收者执行与请求相关的操作，它具体实现对请求的业务处理。

# 适用场景

- 系统需要将请求调用者和请求接收者解耦，使得调用者和接收者不直接交互。请求调用者无须知道接收者的存在，也无须知道接收者是谁，接收者也无须关心何时被调用。
- 系统需要在不同的时间指定请求、将请求排队和执行请求。一个命令对象和请求的初始调用者可以有不同的生命期。
- 系统需要支持命令的撤销(Undo)操作和恢复(Redo)操作。
- 系统需要将一组操作组合在一起形成宏命令。

# 案例

UML类图

![](https://i.imgur.com/z8NiaKT.png)

FBSettingWindow是"功能键设置"界面类，FunctionButton充当请求调用者，Command充当抽象命令类，MinimizeCommand和HelpCommand充当具体命令类，WindowHanlder和HelpHandler充当请求接收者。

功能键设置窗口类:

```java
public class FBSettingWindow {
    private String title; //窗口标题
    //定义一个ArrayList来存储所有功能键
    private List<FunctionButton> functionButtons = new ArrayList<>();

    public FBSettingWindow(String title) {
        this.title = title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getTitle() {
        return this.title;
    }

    public void addFunctionButton(FunctionButton fb) {
        functionButtons.add(fb);
    }

    public void removeFunctionButton(FunctionButton fb) {
        functionButtons.remove(fb);
    }

    //显示窗口及功能键
    public void display() {
        System.out.println("显示窗口：" + this.title);
        System.out.println("显示功能键：");
        for (Object obj : functionButtons) {
            System.out.println(((FunctionButton) obj).getName());
        }
        System.out.println("------------------------------");
    }
}
```

功能键类:

```java
public class FunctionButton {
    private String name; //功能键名称
    private Command command; //维持一个抽象命令对象的引用

    public FunctionButton(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }

    //为功能键注入命令
    public void setCommand(Command command) {
        this.command = command;
    }

    //发送请求的方法
    public void onClick() {
        System.out.print("点击功能键：");
        command.execute();
    }
}
```

命令类:

```java
//抽象命令类
public abstract class Command {
    public abstract void execute();
}
//帮助命令类
public class HelpCommand extends Command {
    private HelpHandler hhObj; //维持对请求接收者的引用

    public HelpCommand() {
        hhObj = new HelpHandler();
    }

    //命令执行方法，将调用请求接收者的业务方法
    @Override
    public void execute() {
        hhObj.display();
    }
}
//最小化命令类
public class MinimizeCommand extends Command {
    private WindowHanlder whObj; //维持对请求接收者的引用

    public MinimizeCommand() {
        whObj = new WindowHanlder();
    }

    //命令执行方法，将调用请求接收者的业务方法
    @Override
    public void execute() {
        whObj.minimize();
    }
}
```

处理类:

```java
//窗口处理类
public class WindowHanlder {
    public void minimize() {
        System.out.println("将窗口最小化至托盘！");
    }
}
//帮助文档处理类
public class HelpHandler {
    public void display() {
        System.out.println("显示帮助文档！");
    }
}
```

客户端:

```java
public class Client {
    public static void main(String args[]) {
        FBSettingWindow fbsw = new FBSettingWindow("功能键设置");

        FunctionButton fb1, fb2;
        fb1 = new FunctionButton("功能键1");
        fb2 = new FunctionButton("功能键1");

        Command command1, command2;
        //通过读取配置文件和反射生成具体命令对象
        command1 = new HelpCommand();
        command2 = new MinimizeCommand();

        //将命令对象注入功能键
        fb1.setCommand(command1);
        fb2.setCommand(command2);

        fbsw.addFunctionButton(fb1);
        fbsw.addFunctionButton(fb2);
        fbsw.display();

        //调用功能键的业务方法
        fb1.onClick();
        fb2.onClick();
    }
}
```

输出结果:

```
显示窗口：功能键设置
显示功能键：
功能键1
功能键1
------------------------------
点击功能键：显示帮助文档！
点击功能键：将窗口最小化至托盘！
```

如果需要修改功能键的功能，例如某个功能键可以实现“自动截屏”，只需要对应增加一个新的具体命令类，在该命令类与屏幕处理者(ScreenHandler)之间创建一个关联关系，然后将该具体命令类的对象通过配置文件注入到某个功能键即可，原有代码无须修改，符合“开闭原则”。在此过程中，每一个具体命令类对应一个请求的处理者（接收者），通过向请求发送者注入不同的具体命令对象可以使得相同的发送者对应不同的接收者，从而实现“将一个请求封装为一个对象，用不同的请求对客户进行参数化”，客户端只需要将具体命令对象作为参数注入请求发送者，无须直接操作请求的接收者。

# 优缺点

## 优点

- **降低系统的耦合度**。由于请求者与接收者之间不存在直接引用，因此请求者与接收者之间实现完全解耦，相同的请求者可以对应不同的接收者，同样，相同的接收者也可以供不同的请求者使用，两者之间具有良好的独立性。
- 新的命令可以很容易地加入到系统中。由于增加新的具体命令类不会影响到其他类，因此增加新的具体命令类很容易，无须修改原有系统源代码，甚至客户类代码，**满足开闭原则的要求**。
- 可以比较容易地设计一个[命令队列](https://gof.quanke.name/%E8%AF%B7%E6%B1%82%E5%8F%91%E9%80%81%E8%80%85%E4%B8%8E%E6%8E%A5%E6%94%B6%E8%80%85%E8%A7%A3%E8%80%A6%E2%80%94%E2%80%94%E5%91%BD%E4%BB%A4%E6%A8%A1%E5%BC%8F%EF%BC%88%E4%B8%89%EF%BC%89.html)或[宏命令（组合命令）](https://gof.quanke.name/%E8%AF%B7%E6%B1%82%E5%8F%91%E9%80%81%E8%80%85%E4%B8%8E%E6%8E%A5%E6%94%B6%E8%80%85%E8%A7%A3%E8%80%A6%E2%80%94%E2%80%94%E5%91%BD%E4%BB%A4%E6%A8%A1%E5%BC%8F%EF%BC%88%E5%85%AD%EF%BC%89.html)。
- 为请求的[撤销(Undo)和恢复(Redo)操作](https://gof.quanke.name/%E8%AF%B7%E6%B1%82%E5%8F%91%E9%80%81%E8%80%85%E4%B8%8E%E6%8E%A5%E6%94%B6%E8%80%85%E8%A7%A3%E8%80%A6%E2%80%94%E2%80%94%E5%91%BD%E4%BB%A4%E6%A8%A1%E5%BC%8F%EF%BC%88%E5%9B%9B%EF%BC%89.html)提供了一种设计和实现方案。

## 缺点

使用命令模式可能会导致某些系统有过多的具体命令类。因为针对每一个对请求接收者的调用操作都需要设计一个具体命令类，因此在某些系统中可能需要提供大量的具体命令类，这将影响命令模式的使用。