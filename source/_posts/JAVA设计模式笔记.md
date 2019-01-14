---
title: JAVA设计模式笔记
date: 2019-01-14 19:19:16
tags:
  - java
  - 设计模式
---
# 设计模式原则

- 单一职责原则 (Single Responsibility Principle, SRP)
- 开闭原则 (Open-Closed Principle, OCP)
- 里氏代换原则 (Liskov Substitution Principle, LSP)
- 依赖倒转原则 (Dependence Inversion Principle, DIP)
- 接口隔离原则 (Interface Segregation Principle, ISP)
- 合成复用原则 (Composite Reuse Principle, CRP)
- 迪米特法则 (Law of Demeter, LoD)

<!-- more -->

# 六个创建型模式

## 简单工厂模式-Simple Factory Pattern

### 概述

定义一个工厂类，它可以根据参数的不同返回不同类的实例，被创建的实例通常都具有共同的父类。因为在简单工厂模式中用于创建实例的方法是静态(static)方法，因此简单工厂模式又被称为静态工厂方法(Static Factory Method)模式

### 组成

- 工厂类角色:这是本模式的核心,含有一定的商业逻辑和判断逻辑。在java中它往往由 一个具体类实现。(ProductFactory)
- 抽象产品角色:它一般是具体产品继承的父类或者实现的接口。在java中由接口或者抽象类来实现。(Product)
- 具体产品角色:工厂类所创建的对象就是此角色的实例。在java中由一个具体类实现。 来用类图来清晰的表示下的它们之间的关系。(ConcreteProductA,B,C)

### 适用场景

- 工厂类负责创建的对象比较少，由于创建的对象较少，不会造成工厂方法中的业务逻辑太过复杂
- 客户端只知道传入工厂类的参数，对于如何创建对象并不关心

### UML类图

![](/JAVA设计模式笔记/20190114113455395.png)

### 代码示例

创建产品接口类:

```java
public interface Product {
}
```

创建三个具体产品类实现Product接口:

```java
public class ConcreteProductA implements Product {
}
public class ConcreteProductB implements Product {
}
public class ConcreteProductC implements Product {
}
```

生产产品的工厂类 (简单的工厂类/静态工厂类):

```java
public class ProductFactory {

    public static Product createProduct(String productType) {
        Product product = null;

        if ("A".equals(productType)) {
            product = new ConcreteProductA();
            System.out.println("工厂 创建了产品对象：ConcreteProductA");
        } else if ("B".equals(productType)) {
            product = new ConcreteProductB();
            System.out.println("工厂 创建了产品对象：ConcreteProductB");
        } else if ("C".equals(productType)) {
            product = new ConcreteProductC();
            System.out.println("工厂 创建了产品对象：ConcreteProductC");
        } else {
            System.out.println("没有该类型的产品，生产产品哪家强 ? 工厂方法模式  : 抽象工厂模式");
        }
        return product;
    }
}
```

客户端调用工厂类静态方法创建产品:

```java
public class Client {
    public static void main(String[] args) {
        ProductFactory.createProduct("A");
        ProductFactory.createProduct("C");
        ProductFactory.createProduct("B");
        ProductFactory.createProduct("D");
    }
}
```

输出结果:

```java
工厂 创建了产品对象：ConcreteProductA
工厂 创建了产品对象：ConcreteProductC
工厂 创建了产品对象：ConcreteProductB
没有该类型的产品，生产产品哪家强 ? 工厂方法模式  : 抽象工厂模式
```

### 优缺点

#### 优点

- 简单工厂模式实现了对象创建和使用的分离。
- 客户端无须知道所创建的具体产品类的类名，只需要知道具体产品类所对应的参数即可，对于一些复杂的类名，通过简单工厂模式可以在一定程度减少使用者的记忆量。
- 通过引入配置文件，可以在不修改任何客户端代码的情况下更换和增加新的具体产品类，在一定程度上提高了系统的灵活性。

#### 缺点

- 由于工厂类集中了所有产品的创建逻辑，职责过重，一旦不能正常工作，整个系统都要受到影响。
- 使用简单工厂模式势必会增加系统中类的个数（引入了新的工厂类），增加了系统的复杂度和理解难度。
- 系统扩展困难，一旦添加新产品就不得不修改工厂逻辑，在产品类型较多时，有可能造成工厂逻辑过于复杂，不利于系统的扩展和维护。
- 简单工厂模式由于使用了静态工厂方法，造成工厂角色无法形成基于继承的等级结构。

## 工厂方法模式-Factory Method Pattern

### 概述

工厂方法模式，又称工厂模式、多态工厂模式和虚拟构造器模式，通过定义工厂父类负责定义创建对象的公共接口，而子类则负责生成具体的对象。

### 组成

- 抽象产品(BaseProduct)
- 抽象工厂(AbstractProductFactory)
- 具体产品(ProductA,B,C)
- 具体工厂(FactoryA,B,C)

### 适用场景

- 当一个类不知道它所需要的对象的类时
- 当一个类希望通过其子类来指定创建对象时
- 将创建对象的任务委托给多个工厂子类中的某一个，客户端在使用时可以无须关心是哪一个工厂子类创建产品子类，需要时再动态指定，可将具体工厂类的类名存储在配置文件或数据库中。

### UML类图

![](https://i.loli.net/2019/01/14/5c3ca9c875a43.png)

### 代码示例

创建抽象工厂类，定义具体工厂的公共接口:

```java
public abstract class AbstractProductFactory {
    public abstract BaseProduct Manufacture();
}
```

创建抽象产品类 ，定义具体产品的公共接口:

```java
public abstract class BaseProduct {
    public abstract void Show();
}
```

创建具体产品类（继承抽象产品类）,定义生产的具体产品:

```java
public class ProductA extends BaseProduct {
    @Override
    public void Show() {
        System.out.println("生产出了产品A");
    }
}
public class ProductB extends BaseProduct {
    @Override
    public void Show() {
        System.out.println("生产出了产品B");
    }
}
```

创建具体工厂类（继承抽象工厂类）,定义创建对应具体产品实例的方法:

```java
public class FactoryA extends AbstractProductFactory {
    @Override
    public BaseProduct Manufacture() {
        return new ProductA();
    }
}
public class FactoryB extends AbstractProductFactory {
    @Override
    public BaseProduct Manufacture() {
        return new ProductB();
    }
}
```

外界通过调用具体工厂类的方法，从而创建不同具体产品类的实例:

```java
public class Test {
    public static void main(String[] args) {
        //客户要产品A
        FactoryA mFactoryA = new FactoryA();
        mFactoryA.Manufacture().Show();

        //客户要产品B
        FactoryB mFactoryB = new FactoryB();
        mFactoryB.Manufacture().Show();
    }
}
```

输出结果:

```java
生产出了产品A
生产出了产品B
```

### 优缺点

#### 优点

- 更符合开-闭原则:新增一种产品时，只需要增加相应的具体产品类和相应的工厂子类即可
- 符合单一职责原则:每个具体工厂类只负责创建对应的产品
- 不使用静态工厂方法，可以形成基于继承的等级结构

#### 缺点

- 在添加新产品时，需要编写新的具体产品类，而且还要提供与之对应的具体工厂类，系统中类的个数将成对增加，在一定程度上增加了系统的复杂度，有更多的类需要编译和运行，会给系统带来一些额外的开销。
- 由于考虑到系统的可扩展性，需要引入抽象层，在客户端代码中均使用抽象层进行定义，增加了系统的抽象性和理解难度，且在实现时可能需要用到DOM、反射等技术，增加了系统的实现难度。

## 抽象工厂模式-Abstract Factory Pattern

### 概述

提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们具体的类。抽象工厂模式又称为Kit模式，它是一种对象创建型模式。

### 组成

- 抽象工厂(AbstractFactory):用于声明生成抽象产品的方法。
- 抽象产品(AbstractProduct):为每种产品声明接口，在抽象产品中定义了产品的抽象业务方法。
- 具体工厂(ConcreteFactory):实现了抽象工厂声明的生成抽象产品的方法，生成一组具体产品，这些产品构成了一个产品族，每一个产品都位于某个产品等级结构中。
- 具体产品(Product):定义具体工厂生产的具体产品对象，实现抽象产品接口中定义的业务方法。

### 适用场景

- 一个系统不应当依赖于产品类实例如何被创建、组合和表达的细节，这对于所有类型的工厂模式都是重要的。
- 系统中有多于一个的产品族，而每次只使用其中某一产品族。
- 属于同一个产品族的产品将在一起使用，这一约束必须在系统的设计中体现出来。
- 系统提供一个产品类的库，所有的产品以同样的接口出现，从而使客户端不依赖于具体实现。

### UML类图

![](https://i.loli.net/2019/01/14/5c3ca9cf6356e.png)

### 代码示例

创建抽象工厂:

```java
public abstract class AbstractFactory {
    abstract void createProductA();
    abstract void createProductB();
}
```

创建抽象产品A,B

```java
public abstract class AbstractProductA {
    abstract void use();
}
public abstract class AbstractProductB {
    abstract void eat();
}
```

创建具体产品A1，A2，B1，B2

```java
public class ProductA1 extends AbstractProductA {
    @Override
    void use() {
        System.out.println("产品A1的use方法");
    }
}
public class ProductA2 extends AbstractProductA {
    @Override
    void use() {
        System.out.println("产品A2的use方法");
    }
}
public class ProductB1 extends AbstractProductB {
    @Override
    void eat() {
        System.out.println("产品B1的eat方法");
    }
}
public class ProductB2 extends AbstractProductB {
    @Override
    void eat() {
        System.out.println("产品B2的eat方法");
    }
}
```

创建具体工厂A,B

```java
public class ConcreateFactory1 extends AbstractFactory {
    @Override
    AbstractProductA createProductA() {
        return new ProductA1();
    }

    @Override
    AbstractProductB createProductB() {
        return new ProductB1();
    }
}
public class ConcreateFactory2 extends AbstractFactory {
    @Override
    AbstractProductA createProductA() {
        return new ProductA2();
    }

    @Override
    AbstractProductB createProductB() {
        return new ProductB2();
    }
}
```

创建测试类:

```java
public class Test {
    public static void main(String[] args) {
        AbstractFactory factory1 = new ConcreateFactory1();
        AbstractFactory factory2 = new ConcreateFactory2();
        factory1.createProductA().use();
        factory1.createProductB().eat();
        factory2.createProductA().use();
        factory2.createProductB().eat();
    }
}
```

输出结果:

```java
产品A1的use方法
产品B1的eat方法
产品A2的use方法
产品B2的eat方法
```

### 优缺点

#### 优点

- 抽象工厂模式隔离了具体类的生成，使得客户并不需要知道什么被创建。由于这种隔离，更换一个具体工厂就变得相对容易。所有的具体工厂都实现了抽象工厂中定义的那些公共接口，因此只需改变具体工厂的实例，就可以在某种程度上改变整个软件系统的行为。另外，应用抽象工厂模式可以实现高内聚低耦合的设计目的，因此抽象工厂模式得到了广泛的应用。
- 当一个产品族中的多个对象被设计成一起工作时，它能够保证客户端始终只使用同一个产品族中的对象。这对一些需要根据当前环境来决定其行为的软件系统来说，是一种非常实用的设计模式。
- 增加新的具体工厂和产品族很方便，无须修改已有系统，符合“开闭原则”。

#### 缺点

- 在添加新的产品对象时，难以扩展抽象工厂来生产新种类的产品，这是因为在抽象工厂角色中规定了所有可能被创建的产品集合，要支持新种类的产品就意味着要对该接口进行扩展，而这将涉及到对抽象工厂角色及其所有子类的修改，显然会带来较大的不便。
- 开闭原则的倾斜性（增加新的工厂和产品族容易，增加新的产品等级结构麻烦）。

## 单例模式-Singleton Pattern

### 概述

单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例，这个类称为单例类，它提供全局访问的方法。

### 组成

- Singleton：单例

### UML类图

![](https://i.loli.net/2019/01/14/5c3ca9c87c39b.png)

### 适用场景

- 系统只需要一个实例对象，如系统要求提供一个唯一的序列号生成器，或者需要考虑资源消耗太大而只允许创建一个对象。
- 客户调用类的单个实例只允许使用一个公共访问点，除了该公共访问点，不能通过其他途径访问该实例。
- 在一个系统中要求一个类只有一个实例时才应当使用单例模式。反过来，如果一个类可以有几个实例共存，就需要对单例模式进行改进，使之成为多例模式。



### 代码示例

创建单例:

```java
public class Singleton {
    private static Singleton instance;

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }

    public void show() {
        System.out.println("show");
    }
    private Singleton() {}

}
```

创建测试类:

```java
public class Test {
    public static void main(String[] args) {
        Singleton.getInstance().show();
    }
}
```

输出结果:

```java
show
```

### 优缺点

#### 优点

- 提供了对唯一实例的受控访问。因为单例类封装了它的唯一实例，所以它可以严格控制客户怎样以及何时访问它，并为设计及开发团队提供了共享的概念。
- 由于在系统内存中只存在一个对象，因此可以节约系统资源，对于一些需要频繁创建和销毁的对象，单例模式无疑可以提高系统的性能。
- 允许可变数目的实例。我们可以基于单例模式进行扩展，使用与单例控制相似的方法来获得指定个数的对象实例。

#### 缺点

- 由于单例模式中没有抽象层，因此单例类的扩展有很大的困难。
- 单例类的职责过重，在一定程度上违背了“单一职责原则”。因为单例类既充当了工厂角色，提供了工厂方法，同时又充当了产品角色，包含一些业务方法，将产品的创建和产品的本身的功能融合到一起。

### 扩展

上面的是懒汉单例模式

#### 饿汉单例模式

饿汉式单例是指在方法调用前，实例就已经创建好了。

```java

public class MySingleton {
	
	private static MySingleton instance = new MySingleton();
	
	private MySingleton(){}
	
	public static MySingleton getInstance() {
		return instance;
	}
}
```

#### 单例模式的线程安全问题

##### 使用synchronized关键字(效率低下)

对singleton对象使用volatile关键字进行限制，保证其对所有线程的可见性，并且禁止对其进行指令重排序优化。

```java
public class Singleton1 {
    private static volatile Singleton1 instance = null;

    private Singleton1() {
    }

    public static Singleton1 getInstance() {
        synchronized (Singleton1.class) {
            if (instance == null) {
                instance = new Singleton1();
            }
            return instance;
        }
    }
}
```

##### 兼顾线程安全和效率(推荐)

```java
public class Singleton1 {
    private static volatile Singleton1 singleton = null;

    private Singleton1() {
    }

    public static Singleton1 getSingleton() {
        if (singleton == null) {
            synchronized (Singleton.class) {
                if (singleton == null) {
                    singleton = new Singleton1();
                }
            }
        }
        return singleton;
    }
}
```

##### 静态内部类法

把Singleton实例放到一个静态内部类中，这样就避免了静态实例在Singleton类加载的时候就创建对象，并且由于静态内部类只会被加载一次，所以这种写法也是线程安全的。

```java
public class Singleton {
    private static class SingletonInner {
        private static Singleton singleton = new Singleton();
    }

    private Singleton() {
    }

    public static Singleton getSingleton() {
        return SingletonInner.singleton;
    }

}
```

##### 枚举实现单例模式

使用枚举除了线程安全和防止反射强行调用构造器之外，还提供了自动序列化机制，防止反序列化的时候创建新的对象。因此，Effective Java推荐尽可能地使用枚举来实现单例。

```JAVA
public enum Singleton {
    INSTANCE;
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```










