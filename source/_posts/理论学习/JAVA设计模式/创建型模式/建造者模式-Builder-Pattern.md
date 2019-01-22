---
title: 建造者模式-Builder Pattern
date: 2019-01-15 16:01:37
tags:
  - java
  - 设计模式
categories: 
  - 理论学习
  - JAVA设计模式
---

# 概述
> 建造者模式，又称生成器模式：将一个复杂的构建与其表示相分离，使得同样的构建过程可以创建不同的表示。

<!-- more -->

# 组成

-  建造者角色
> 定义生成实例所需要的所有方法
-  具体的建造者角色
> 实现生成实例所需要的所有方法，并且定义获取最终生成实例的方法
-  监工角色
> 定义使用建造者角色中的方法来生成实例的方法
-  使用者
> 使用建造者模式

# 适用场景

- 需要生成的对象具有复杂的内部结构
- 需要生成的对象内部属性本身相互依赖

# 案例

UML类图

![](https://i.loli.net/2019/01/15/5c3da07985517.png)

```java
//建造者
public abstract class Builder {
    public abstract void buildPart1();

    public abstract void buildPart2();

    public abstract void buildPart3();
}
// 将一个复杂的构建过程与其表示相分离
public class Director {
    // 针对接口编程，而不是针对实现编程
    private Builder builder;

    public Director(Builder builder) {
        this.builder = builder;
    }

    public void setBuilder(Builder builder) {
        this.builder = builder;
    }

    // 控制（定义）一个复杂的构建过程
    public void construct() {
        builder.buildPart1();
        // 提示：如果想在运行过程中替换构建算法，可以考虑结合策略模式。
        for (int i = 0; i < 5; i++) {
            builder.buildPart2();
        }
        builder.buildPart3();
    }
}
//具体的建造者
public class ConcreteBuilder1 extends Builder {
    private StringBuffer buffer = new StringBuffer();//假设 buffer.toString() 就是最终生成的产品

    @Override
    public void buildPart1() {//实现构建最终实例需要的所有方法
        buffer.append("Builder1 : Part1\n");
    }

    @Override
    public void buildPart2() {
        buffer.append("Builder1 : Part2\n");
    }

    @Override
    public void buildPart3() {
        buffer.append("Builder1 : Part3\n");
    }

    public String getResult() {//定义获取最终生成实例的方法
        return buffer.toString();
    }
}
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        ConcreteBuilder1 b1 = new ConcreteBuilder1();//建造者
        Director director = new Director(b1);//监工
        director.construct();//建造实例(监工负责监督，建造者实际建造)
        String result = b1.getResult();//获取最终生成结果
        System.out.printf("the result is :%n%s", result);
    }
}
```

# 优缺点

## 优点

- 建造者独立，易扩展
- 便于控制细节风险

## 缺点

- 产品必须有共同点，范围有限制
- 如内部变化复杂，会有很多的建造类

# 扩展

## 使用[Lombok](https://juejin.im/post/5a6eceb8f265da3e467555fe)插件自动生成Builder建造者

使用@Builder注解修饰Class:

```java
@Builder
public class User {
    private String username;
    private String password;
    private Integer age;
}
```

测试一下用法:

```java
public class Test {
    public static void main(String[] args) {
        User user = User.builder()
                .username("小明")
                .password("123456")
                .age(20).build();
        System.out.println(user);
    }
}
```

输出结果:

```
User(username=小明, password=123456, age=20)
```

# 练习
> Sunny软件公司欲开发一个视频播放软件，为了给用户使用提供方便，该播放软件提供多种界面显示模式，如完整模式、精简模式、记忆模式、网络模式等。在不同的显示模式下主界面的组成元素有所差异，如在完整模式下将显示菜单、播放列表、主窗口、控制条等，在精简模式下只显示主窗口和控制条，而在记忆模式下将显示主窗口、控制条、收藏列表等。尝试使用建造者模式设计该软件。

```java
//抽象建造者
public abstract class AbstractBuilder {
    protected static StringBuffer sb = new StringBuffer(); //模拟视频播放软件

    public abstract void memu(); //菜单
    public abstract void playlist(); //播放列表
    public abstract void mainWindow(); //主窗口
    public abstract void controlStrip(); //控制条
    public abstract void favoritesList(); //收藏列表

    //完整模式
    public static StringBuffer full(AbstractBuilder builder) {
        builder.memu();
        builder.playlist();
        builder.mainWindow();
        builder.controlStrip();
        return sb;
    }

    //精简模式
    public static StringBuffer simplify(AbstractBuilder builder) {
        builder.mainWindow();
        builder.controlStrip();
        return sb;
    }

    //记忆模式
    public static StringBuffer memory(AbstractBuilder builder) {
        builder.mainWindow();
        builder.controlStrip();
        builder.favoritesList();
        return sb;
    }

}
//具体建造者
public class RealBuilder extends AbstractBuilder {
    @Override
    public void memu() {
        sb.append("菜单,");
    }

    @Override
    public void playlist() {
        sb.append("播放列表,");
    }

    @Override
    public void mainWindow() {
        sb.append("主窗口,");
    }

    @Override
    public void controlStrip() {
        sb.append("控制条,");
    }

    @Override
    public void favoritesList() {
        sb.append("收藏列表,");
    }
}
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        AbstractBuilder builder = new RealBuilder();
        AbstractBuilder.full(builder);
        System.out.println(AbstractBuilder.sb.toString()); //完整模式

        AbstractBuilder.sb = new StringBuffer(); //清空原来的产品
        AbstractBuilder.memory(builder);
        System.out.println(AbstractBuilder.sb.toString()); //记忆模式
    }
}
```

输出结果:

```
菜单,播放列表,主窗口,控制条,
主窗口,控制条,收藏列表,
```

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/buildPattern)
👉 [返回设计模式概览](../../设计模式概览)