---
title: 桥接模式-Bridge Pattern
date: 2019-01-15 18:16:08
tags:
  - java
  - 设计模式
categories: 
  - 理论学习
  - JAVA设计模式
---

# 概述
> 桥接模式基于类的最小设计原则，通过使用封装，聚合以及继承等行为来让不同的类承担不同的责任。它的主要特点是把抽象（abstraction）与行为实现（implementation）分离开来，从而可以保持各部分的独立性以及应对它们的功能扩展。

<!-- more -->

# 适用场景

处理多层继承结构，多维度变化的场景。将各个维度设计成独立的继承结构，使得各个维度可以独立的扩展。

# 案例

以电脑大卖场为例，电脑商品可简单用下图表示:

![](https://i.loli.net/2019/01/15/5c3dbf7e7735c.png)

图中将电脑结构分成 3 级，第 1 级就是商品名称（电脑），第 2 级是电脑类型分类，第 3 级是品牌分类。
我们从 2 个维度分类，将它们分离独立出来，即将电脑类型和电脑品牌分离出来。

UML类图

![](https://i.loli.net/2019/01/15/5c3dbf7e5d6bb.png)

```java
//品牌维度
public interface Brand {
    void info();
}
class LenovoBrand implements Brand {
    @Override
    public void info() {
        System.out.println("联想");
    }
    
}
class AsusBrand implements Brand {
    
    @Override
    public void info() {
        System.out.println("华硕");
    }
    
}
//类型维度
public abstract class Computer {
    protected Brand brand;
    public Computer(Brand brand) {
        this.brand = brand;
    }
    
    public void info() {
        this.brand.info();
    }
}
class Desktop extends Computer {
    public Desktop(Brand brand) {
        super(brand);
    }
    
    public void info() {
        super.info();
        System.out.println("台式电脑");
    }
    
}
class Laptop extends Computer {
    public Laptop(Brand brand) {
        super(brand);
    }
    
    public void info() {
        super.info();
        System.out.println("笔记本电脑");
    }
    
}
```

客户端：

```java
public class Client {
    public static void main(String[] args) {
        Computer computer = new Desktop(new LenovoBrand());
        computer.info();
    }
}
```

结果输出:

```
联想
台式电脑
```

# 反例

传统的继承方法实现上面的电脑案例有什么缺点?

```java
public interface Computer {
    void info();
}
class Desktop implements Computer {
    @Override
    public void info() {
        System.out.println("台式电脑！");
    }
}
class Laptop implements Computer {
    @Override
    public void info() {
        System.out.println("笔记本电脑！");
    }
}
class LenovoDesktop extends Desktop {
    @Override
    public void info() {
        System.out.println("联想台式电脑");
    }
}
class AsusDesktop extends Desktop {
    @Override
    public void info() {
        System.out.println("华硕台式电脑");
    }
}
class LenovoLaptop extends Laptop {
    @Override
    public void info() {
        System.out.println("联想笔记本电脑");
    }
}
class AsusLaptop extends Laptop {
    @Override
    public void info() {
        System.out.println("华硕笔记本电脑");
    }
}
```

## UML类图

![](https://i.loli.net/2019/01/15/5c3dbf7e51939.png)

# 优缺点

## 优点

- 分离抽象接口及其实现部分
> 桥接模式使用“对象间的关联关系”解耦了抽象和实现之间固有的绑定关系，使得抽象和实现可以沿着各自的维度来变化。所谓抽象和实现沿着各自维度的变化，也就是说抽象和实现不再在同一个继承层次结构中，而是“子类化”它们，使它们各自都具有自己的子类，以便任何组合子类，从而获得多维度组合对象。
- 极大减少了子类的个数
> 在很多情况下，桥接模式可以取代多层继承方案，多层继承方案违背了“单一职责原则”，复用性较差，且类的个数非常多，桥接模式是比多层继承方案更好的解决方法，它极大减少了子类的个数。
- 提高了系统的可扩展性
> 桥接模式提高了系统的可扩展性，在两个变化维度中任意扩展一个维度，都不需要修改原有系统，符合“开闭原则”。

## 缺点

- 扩展性问题
> 如果新增平板电脑分类，需要添加 N 个品牌子类。如果新增索尼品牌，其他电脑分类也需要添加相应子类。
- 违背单一职责原则
> 新增类型或新增品牌都会导致第 3 级目录结构的变化。

# 练习
> Sunny软件公司欲开发一个数据转换工具，可以将数据库中的数据转换成多种文件格式，例如txt、xml、pdf等格式，同时该工具需要支持多种不同的数据库。试使用桥接模式对其进行设计。

```java
//模拟数据库取数据
public interface CRUDType {
    String get();
}
//模拟导出
public abstract class Export {
    protected CRUDType crudType;

    public Export(CRUDType crudType) {
        this.crudType = crudType;
    }

    /**
     * 定义导出方法
     */
    public abstract void export();
}
//mysql数据库类型
public class MysqlType implements CRUDType {
    @Override
    public String get() {
        return "mysql取数据";
    }
}
//oracle数据库类型
public class OracleType implements CRUDType {
    @Override
    public String get() {
        return "oracle取数据";
    }
}
//PG数据库类型
public class PGType implements CRUDType {
    @Override
    public String get() {
        return "PG取数据";
    }
}
//PDF导出
public class PDFExport extends Export {
    public PDFExport(CRUDType crudType) {
        super(crudType);
    }

    @Override
    public void export() {
        String data = this.crudType.get();
        System.out.println("导出PDF : " + data);
    }
}
//TXT导出
public class TXTExport extends Export {
    public TXTExport(CRUDType crudType) {
        super(crudType);
    }

    @Override
    public void export() {
        String data = this.crudType.get();
        System.out.println("导出TXT : " + data);
    }
}
//XML导出
public class XMLExport extends Export {
    public XMLExport(CRUDType crudType) {
        super(crudType);
    }

    @Override
    public void export() {
        String data = this.crudType.get();
        System.out.println("导出XML : " + data);
    }
}
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        Export export = new XMLExport(new MysqlType());
        export.export();

        export = new PDFExport(new PGType());
        export.export();
    }
}
```

输出结果:

```
导出XML : mysql取数据
导出PDF : PG取数据
```

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/bridgePattern)
👉 [返回设计模式概览](../../设计模式概览)