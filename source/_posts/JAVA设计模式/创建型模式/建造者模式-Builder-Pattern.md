---
title: 建造者模式-Builder Pattern
date: 2019-01-15 16:01:37
tags:
  - java
  - 设计模式
categories: 
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

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/buildPattern)
👉 [返回设计模式概览](../../设计模式概览)