---
title: 访问者模式-Visitor Pattern
date: 2019-01-19 20:39:39
tags:
  - java
  - 设计模式
categories: 
  - 理论学习
  - JAVA设计模式
---

# 概述
> 提供一个作用于某对象结构中的各元素的操作表示，它使我们可以在不改变各元素的类的前提下定义作用于这些元素的新操作。

<!-- more -->

# 组成

- Vistor（抽象访问者）
> 抽象访问者为对象结构中每一个具体元素类ConcreteElement声明一个访问操作，从这个操作的名称或参数类型可以清楚知道需要访问的具体元素的类型，具体访问者需要实现这些操作方法，定义对这些元素的访问操作。
- ConcreteVisitor（具体访问者）
> 具体访问者实现了每个由抽象访问者声明的操作，每一个操作用于访问对象结构中一种类型的元素。
- Element（抽象元素）
> 抽象元素一般是抽象类或者接口，它定义一个accept()方法，该方法通常以一个抽象访问者作为参数。
- ConcreteElement（具体元素）
> 具体元素实现了accept()方法，在accept()方法中调用访问者的访问方法以便完成对一个元素的操作。
- ObjectStructure（对象结构）
> 对象结构是一个元素的集合，它用于存放元素对象，并且提供了遍历其内部元素的方法。

# 适用场景

- 一个对象结构包含多个类型的对象，希望对这些对象实施一些依赖其具体类型的操作
> 在访问者中针对每一种具体的类型都提供了一个访问操作，不同类型的对象可以有不同的访问操作。
- 需要对一个对象结构中的对象进行很多不同的并且不相关的操作
> 而需要避免让这些操作“污染”这些对象的类，也不希望在增加新操作时修改这些类。访问者模式使得我们可以将相关的访问操作集中起来定义在访问者类中，对象结构可以被多个不同的访问者类所使用，将对象本身与对象的访问操作分离。
- 对象结构中对象对应的类很少改变，但经常需要在此对象结构上定义新的操作

# 案例

> 软件公司开发人员使用访问者模式对OA系统中员工数据汇总模块进行重构，使得系统可以很方便地增加新类型的访问者，更加符合“单一职责原则”和“开闭原则”

```java
//员工类：抽象元素类
public interface Employee {
    void accept(Department handler); //接受一个抽象访问者访问
}
//全职员工类：具体元素类
@Data
@AllArgsConstructor
public class FulltimeEmployee implements Employee {
    private String name;
    private double weeklyWage;
    private int workTime;
    @Override
    public void accept(Department handler) {
        handler.visit(this); //调用访问者的访问方法
    }
}
//兼职员工类：具体元素类
@Data
@AllArgsConstructor
public class ParttimeEmployee implements Employee {
    private String name;
    private double hourWage;
    private int workTime;
    @Override
    public void accept(Department handler) {
        handler.visit(this); //调用访问者的访问方法
    }
}
//部门类：抽象访问者类
public abstract class Department {
    //声明一组重载的访问方法，用于访问不同类型的具体元素
    public abstract void visit(FulltimeEmployee employee);
    public abstract void visit(ParttimeEmployee employee);
}
//财务部类：具体访问者类
public class FADepartment extends Department {
    //实现财务部对全职员工的访问
    @Override
    public void visit(FulltimeEmployee employee) {
        int workTime = employee.getWorkTime();
        double weekWage = employee.getWeeklyWage();
        if (workTime > 40) {
            weekWage = weekWage + (workTime - 40) * 100;
        } else if (workTime < 40) {
            weekWage = weekWage - (40 - workTime) * 80;
            if (weekWage < 0) {
                weekWage = 0;
            }
        }
        System.out.println("正式员工" + employee.getName() + "实际工资为：" + weekWage + "元。");
    }

    //实现财务部对兼职员工的访问
    @Override
    public void visit(ParttimeEmployee employee) {
        int workTime = employee.getWorkTime();
        double hourWage = employee.getHourWage();
        System.out.println("临时工" + employee.getName() + "实际工资为：" + workTime * hourWage + "元。");
    }
}
//人力资源部类：具体访问者类
public class HRDepartment extends Department {
    @Override
    public void visit(FulltimeEmployee employee) {
        int workTime = employee.getWorkTime();
        System.out.println("正式员工" + employee.getName() + "实际工作时间为：" + workTime + "小时。");
        if (workTime > 40) {
            System.out.println("正式员工" + employee.getName() + "加班时间为：" + (workTime - 40) + "小时。");
        } else if (workTime < 40) {
            System.out.println("正式员工" + employee.getName() + "请假时间为：" + (40 - workTime) + "小时。");
        }
    }

    @Override
    public void visit(ParttimeEmployee employee) {
        int workTime = employee.getWorkTime();
        System.out.println("临时工" + employee.getName() + "实际工作时间为：" + workTime + "小时。");
    }
}
//员工列表类：对象结构
public class EmployeeList {
    private List<Employee> list = new ArrayList<>();

    public void addEmployee(Employee employee) {
        list.add(employee);
    }

    //遍历访问员工集合中的每一个员工对象
    public void accept(Department handler) {
        for (Object obj : list) {
            ((Employee) obj).accept(handler);
        }
    }
}
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        EmployeeList list = new EmployeeList();
        Employee fte1, fte2, fte3, pte1, pte2;

        fte1 = new FulltimeEmployee("张无忌", 3200.00, 45);
        fte2 = new FulltimeEmployee("杨过", 2000.00, 40);
        fte3 = new FulltimeEmployee("段誉", 2400.00, 38);
        pte1 = new ParttimeEmployee("洪七公", 80.00, 20);
        pte2 = new ParttimeEmployee("郭靖", 60.00, 18);

        list.addEmployee(fte1);
        list.addEmployee(fte2);
        list.addEmployee(fte3);
        list.addEmployee(pte1);
        list.addEmployee(pte2);

        Department dep = new HRDepartment();
        list.accept(dep);
    }
}
```

输出结果:

```
正式员工张无忌实际工作时间为：45小时。
正式员工张无忌加班时间为：5小时。
正式员工杨过实际工作时间为：40小时。
正式员工段誉实际工作时间为：38小时。
正式员工段誉请假时间为：2小时。
临时工洪七公实际工作时间为：20小时。
临时工郭靖实际工作时间为：18小时。
```

# 优缺点

## 优点

- 增加新的访问操作很方便
> 使用访问者模式，增加新的访问操作就意味着增加一个新的具体访问者类，实现简单，无须修改源代码，符合“开闭原则”。
- 类的职责更加清晰，有利于对象结构中元素对象的复用，相同的对象结构可以供多个不同的访问者访问
> 将有关元素对象的访问行为集中到一个访问者对象中，而不是分散在一个个的元素类中
- 让用户能够在不修改现有元素类层次结构的情况下，定义作用于该层次结构的操作

## 缺点

- 增加新的元素类很困难
> 在访问者模式中，每增加一个新的元素类都意味着要在抽象访问者角色中增加一个新的抽象操作，并在每一个具体访问者类中增加相应的具体操作，这违背了“开闭原则”的要求。
- 破坏封装
> 访问者模式要求访问者对象访问并调用每一个元素对象的操作，这意味着元素对象有时候必须暴露一些自己的内部操作和内部状态，否则无法供访问者访问。

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/visitorPattern)
👉 [返回设计模式概览](../../设计模式概览)