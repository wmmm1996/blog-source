---
title: 原型模式-Prototype Pattern
date: 2019-01-15 10:27:40
tags:
  - JAVA设计模式
categories: 
  - 理论学习
  - JAVA设计模式
---

# 概述
> 使用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。原型模式是一种对象创建型模式。<!-- more -->

# 组成

- **Prototype（抽象原型类）**
> 它是声明克隆方法的接口，是所有具体原型类的公共父类，可以是抽象类也可以是接口，甚至还可以是具体实现类。
- **ConcretePrototype（具体原型类）**
> 它实现在抽象原型类中声明的克隆方法，在克隆方法中返回自己的一个克隆对象。
- **Client（客户类）**
> 让一个原型对象克隆自身从而创建一个新的对象，在客户类中只需要直接实例化或通过工厂方法等方式创建一个原型对象，再通过调用该对象的克隆方法即可得到多个相同的对象。

# 适用场景

- 创建新对象成本较大（如初始化需要占用较长的时间，占用太多的CPU资源或网络资源），新的对象可以通过原型模式对已有对象进行复制来获得，如果是相似对象，则可以对其成员变量稍作修改。
- 如果系统要保存对象的状态，而对象的状态变化很小，或者对象本身占用内存较少时，可以使用原型模式配合备忘录模式来实现。
- 需要避免使用分层次的工厂类来创建分层次的对象，并且类的实例对象只有一个或很少的几个组合状态，通过复制原型对象得到新实例可能比使用构造函数创建一个新实例更加方便。

# 案例

UML类图

![](https://i.imgur.com/CmS8jlL.png)

创建抽象原型类:

```java
@Data
public abstract class Prototype {
    protected String attr;

    @Override
    protected abstract Prototype clone();
}
```

创建具体原型类:

```java
@Data
public class ConcretePrototype extends Prototype {
    @Override
    public Prototype clone() {
        ConcretePrototype prototype = new ConcretePrototype(); //创建新对象
        prototype.setAttr(this.attr);
        return prototype;
    }
}
```

创建客户端类:

```java
public class Client {
    public static void main(String[] args) {
        Prototype obj1 = new ConcretePrototype();
        obj1.setAttr("Sunny");
        Prototype obj2 = obj1.clone();
        System.out.println("obj1 和 obj2 是同一个对象吗? " + (obj1 == obj2));
        System.out.println("obj2 的 attr : " + obj2.getAttr());
    }
}
```

输出结果:

```
obj1 和 obj2 是同一个对象吗? false
Sunny
```

# 优缺点

## 优点

- 当创建新的对象实例较为复杂时，使用原型模式可以简化对象的创建过程，通过复制一个已有实例可以提高新实例的创建效率。
- 扩展性较好，由于在原型模式中提供了抽象原型类，在客户端可以针对抽象原型类进行编程，而将具体原型类写在配置文件中，增加或减少产品类对原有系统都没有任何影响。
- 原型模式提供了简化的创建结构，工厂方法模式常常需要有一个与产品类等级结构相同的工厂等级结构，而原型模式就不需要这样，原型模式中产品的复制是通过封装在原型类中的克隆方法实现的，无须专门的工厂类来创建产品。
- 可以使用深克隆的方式保存对象的状态，使用原型模式将对象复制一份并将其状态保存起来，以便在需要的时候使用（如恢复到某一历史状态），可辅助实现撤销操作。

## 缺点

- 需要为每一个类配备一个克隆方法，而且该克隆方法位于一个类的内部，当对已有的类进行改造时，需要修改源代码，违背了“开闭原则”。
- 在实现深克隆时需要编写较为复杂的代码，而且当对象之间存在多重的嵌套引用时，为了实现深克隆，每一层对象对应的类都必须支持深克隆，实现起来可能会比较麻烦。

# 扩展

## Java语言提供的clone()方法

需要注意的是能够实现克隆的Java类必须实现一个标识接口Cloneable，表示这个Java类支持被复制。如果一个类没有实现这个接口但是调用了clone()方法，Java编译器将抛出一个CloneNotSupportedException异常。

```java
public class ConcretePrototype implements Cloneable {
    @Override
    public ConcretePrototype clone() {
        Object object = null;
        try {
            object = super.clone();
        } catch (CloneNotSupportedException e) {
            System.out.println("Not support cloneable");
        }
        return (ConcretePrototype) object;
    }
}
```

创建client类

```java
public class Client {
    public static void main(String[] args) {
        ConcretePrototype obj1 = new ConcretePrototype();
        ConcretePrototype obj2 = obj1.clone();
        System.out.println("obj1 和 obj2 是同一个对象吗? " + (obj1 == obj2));
    }
}
```

输出结果:

```
obj1 和 obj2 是同一个对象吗? false
```

一般而言，Java语言中的clone()方法满足:

- 对任何对象x，都有x.clone() != x，即克隆对象与原型对象不是同一个对象
- 对任何对象x，都有x.clone().getClass() == x.getClass()，即克隆对象与原型对象的类型一样
- 如果对象x的equals()方法定义恰当，那么x.clone().equals(x)应该成立

## 浅拷贝和深拷贝

### 浅拷贝

在浅拷贝中，当对象被复制时只复制它本身和其中包含的值类型的成员变量，而引用类型的成员对象并没有复制。

![](https://i.imgur.com/hG1w4Sg.png)

### 深拷贝

在深拷贝中，除了对象本身被复制外，对象所包含的所有成员变量也将复制。

![](https://i.imgur.com/Fp32FdS.png)

## 原型管理器(PrototypeManager)的使用

原型管理器(PrototypeManager)的作用是:创建具体原型类的对象，并记录每一个被创建的对象。

### UML类图

![](https://i.imgur.com/DOGUkq4.png)

### 代码示例

创建prototype接口

```java
public interface Prototype {
    public Prototype clone();
    public String getName();
    public void setName(String name);
}
```

创建具体原型类

```java
@Data
public class ConcretePrototype1 implements Prototype {
    private String name;

    @Override
    public Prototype clone() {
        ConcretePrototype1 prototype = new ConcretePrototype1();
        prototype.setName(this.name);
        return prototype;
    }

    @Override
    public String toString(){
        return "Now in Prototype1 , name = " + this.name;
    }
}
@Data
public class ConcretePrototype2 implements Prototype {
    private String name;

    @Override
    public Prototype clone() {
        ConcretePrototype2 prototype = new ConcretePrototype2();
        prototype.setName(this.name);
        return prototype;
    }

    @Override
    public String toString() {
        return "Now in Prototype2 , name = " + this.name;
    }
}
```

原型管理器角色保持一个聚集，作为对所有原型对象的登记，这个角色提供必要的方法，供外界增加新的原型对象和取得已经登记过的原型对象。

```java
public class PrototypeManager {
    /**
     * 用来记录原型的编号和原型实例的对应关系
     */
    private static Map<String, Prototype> map = new HashMap<>();

    /**
     * 私有化构造方法，避免外部创建实例
     */
    private PrototypeManager() {
    }

    /**
     * 向原型管理器里面添加或是修改某个原型注册
     *
     * @param prototypeId 原型编号
     * @param prototype   原型实例
     */
    public synchronized static void setPrototype(String prototypeId, Prototype prototype) {
        map.put(prototypeId, prototype);
    }

    /**
     * 从原型管理器里面删除某个原型注册
     *
     * @param prototypeId 原型编号
     */
    public synchronized static void removePrototype(String prototypeId) {
        map.remove(prototypeId);
    }

    /**
     * 获取某个原型编号对应的原型实例
     *
     * @param prototypeId 原型编号
     * @return 原型编号对应的原型实例
     * @throws Exception 如果原型编号对应的实例不存在，则抛出异常
     */
    public synchronized static Prototype getPrototype(String prototypeId) throws Exception {
        Prototype prototype = map.get(prototypeId);
        if (prototype == null) {
            throw new Exception("您希望获取的原型还没有注册或已被销毁");
        }
        return prototype;
    }
}
```

创建客户端:

```java
public class Client {
    public static void main(String[] args) {
        try {
            Prototype p1 = new ConcretePrototype1();
            PrototypeManager.setPrototype("p1", p1);
            //获取原型来创建对象
            Prototype p3 = PrototypeManager.getPrototype("p1").clone();
            p3.setName("张三");
            System.out.println("第一个实例：" + p3);
            //有人动态的切换了实现
            Prototype p2 = new ConcretePrototype2();
            PrototypeManager.setPrototype("p1", p2);
            //重新获取原型来创建对象
            Prototype p4 = PrototypeManager.getPrototype("p1").clone();
            p4.setName("李四");
            System.out.println("第二个实例：" + p4);
            //有人注销了这个原型
            PrototypeManager.removePrototype("p1");
            //再次获取原型来创建对象
            Prototype p5 = PrototypeManager.getPrototype("p1").clone();
            p5.setName("王五");
            System.out.println("第三个实例：" + p5);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

[返回设计模式概览](#理论学习/JAVA设计模式/设计模式概览)