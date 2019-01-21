---
title: 装饰模式-Decorator Pattern
date: 2019-01-15 21:46:56
tags:
  - java
  - 设计模式
categories: 
  - JAVA设计模式
---

# 概述
> 动态地给一个对象增加一些额外的职责，就增加对象功能来说，装饰模式比生成子类实现更为灵活。

<!-- more -->

# 组成

- Component（抽象构件）
> 它是具体构件和抽象装饰类的共同父类，声明了在具体构件中实现的业务方法，它的引入可以使客户端以一致的方式处理未被装饰的对象以及装饰之后的对象，实现客户端的透明操作。
- ConcreteComponent（具体构件）
> 它是抽象构件类的子类，用于定义具体的构件对象，实现了在抽象构件中声明的方法，装饰器可以给它增加额外的职责(方法)。
- Decorator（抽象装饰类）
> 它也是抽象构件类的子类，用于给具体构件增加职责，但是具体职责在其子类中实现。它维护一个指向抽象构件对象的引用，通过该引用可以调用装饰之前构件对象的方法，并通过其子类扩展该方法，以达到装饰的目的。
- ConcreteDecorator（具体装饰类）
> 它是抽象装饰类的子类，负责向构件添加新的职责。每一个具体装饰类都定义了一些新的行为，它可以调用在抽象装饰类中定义的方法，并可以增加新的方法用以扩充对象的行为。

# 适用场景

- 在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责。
- 当不能采用继承的方式对系统进行扩展或者采用继承不利于系统扩展和维护时可以使用装饰模式。不能采用继承的情况主要有两类：第一类是系统中存在大量独立的扩展，为支持每一种扩展或者扩展之间的组合将产生大量的子类，使得子类数目呈爆炸性增长；第二类是因为类已定义为不能被继承（如Java语言中的final类）。

# 案例

UML类图

![](https://i.imgur.com/mlnhbhf.png)

```java
//抽象构件类
public abstract class Component {
    public abstract void display();
}
//具体构件类
public class Window extends Component {
    @Override
    public void display() {
        System.out.println("显示窗体！");
    }
}
public class TextBox extends Component {
    @Override
    public void display() {
        System.out.println("显示文本框！");
    }
}
public class ListBox extends Component {
    @Override
    public void display() {
        System.out.println("显示列表框！");
    }
}
//抽象装饰类
public class ComponentDecorator extends Component {

    private Component component;  //维持对抽象构件类型对象的引用

    public ComponentDecorator(Component component) {
        this.component = component;
    }

    @Override
    public void display() {
        component.display();
    }
}
//具体装饰类
public class ScrollBarDecorator extends ComponentDecorator {
    public ScrollBarDecorator(Component component) {
        super(component);
    }

    @Override
    public void display() {
        this.setScrollBar();
        super.display();
    }

    public void setScrollBar() {
        System.out.println("为构件增加滚动条！");
    }
}
public class BlackBorderDecorator extends ComponentDecorator {
    public BlackBorderDecorator(Component component) {
        super(component);
    }

    @Override
    public void display() {
        this.setBlackBorder();
        super.display();
    }

    public void setBlackBorder() {
        System.out.println("为构件增加黑色边框！");
    }
}
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        Component component, componentSB, componentBB; //全部使用抽象构件定义
        component = new Window();
        componentSB = new ScrollBarDecorator(component);
        componentBB = new BlackBorderDecorator(componentSB); //将装饰了一次之后的对象继续注入到另一个装饰类中，进行第二次装饰
        componentBB.display();
    }
}
```

输出结果:

```
为构件增加黑色边框！
为构件增加滚动条！
显示窗体！
```

# 优缺点

## 优点

- 不会导致类的个数急剧增加
> 对于扩展一个对象的功能，装饰模式比继承更加灵活性，不会导致类的个数急剧增加。
- 动态扩展一个对象的功能
> 通过配置文件可以在运行时选择不同的具体装饰类，从而实现不同的行为。
- 可以对一个对象进行多次装饰
> 通过使用不同的具体装饰类以及这些装饰类的排列组合，可以创造出很多不同行为的组合，得到功能更为强大的对象。
- 具体构件类与具体装饰类可以独立变化
> 用户可以根据需要增加新的具体构件类和具体装饰类，原有类库代码无须改变，符合"开闭原则"。

## 缺点

- 占用系统资源，影响程序性能
> 使用装饰模式进行系统设计时将产生很多小对象，这些对象的区别在于它们之间相互连接的方式有所不同，而不是它们的类或者属性值有所不同，大量小对象的产生势必会占用更多的系统资源，在一定程序上影响程序的性能。
- 程序易出错，且排错较困难
> 装饰模式提供了一种比继承更加灵活机动的解决方案，但同时也意味着比继承更加易于出错，排错也很困难，对于多次装饰的对象，调试时寻找错误可能需要逐级排查，较为繁琐。

# 练习
> Sunny软件公司欲开发了一个数据加密模块，可以对字符串进行加密。最简单的加密算法通过对字母进行移位来实现，同时还提供了稍复杂的逆向输出加密，还提供了更为高级的求模加密。用户先使用最简单的加密算法对字符串进行加密，如果觉得还不够可以对加密之后的结果使用其他加密算法进行二次加密，当然也可以进行第三次加密。试使用装饰模式设计该多重加密系统。

## UML类图

![](https://i.imgur.com/P0SEAjo.png)

```java
//抽象构件
public interface IEncrypt {
    String encrypt(String data);
}
//抽象加密装饰者
@AllArgsConstructor
public class EncryptDecorator implements IEncrypt {
    protected IEncrypt encrypt;

    @Override
    public String encrypt(String data) {
        return encrypt.encrypt(data);
    }
}
//简单加密
public class SimpleEncrypt implements IEncrypt {
    @Override
    public String encrypt(String data) {
        return data + " + 简单加密";
    }
}
//逆向输出加密
public class ReverseEncryptDecorator extends EncryptDecorator {
    public ReverseEncryptDecorator(IEncrypt encrypt) {
        super(encrypt);
    }

    @Override
    public String encrypt(String data) {
        data = this.encrypt.encrypt(data);
        return data + " + 逆向输出加密";
    }
}
//求模加密
public class ModelEncryptDecorator extends EncryptDecorator {
    public ModelEncryptDecorator(IEncrypt encrypt) {
        super(encrypt);
    }

    @Override
    public String encrypt(String data) {
        data = encrypt.encrypt(data);
        return data + " + 求模加密";
    }
}
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        IEncrypt simpleEncrypt = new SimpleEncrypt();
        IEncrypt reverseEncryptDecorator = new ReverseEncryptDecorator(simpleEncrypt); //逆向加密

        System.out.println(reverseEncryptDecorator.encrypt("两次加密"));

        //多次加密
        IEncrypt modelEncryptDecorator = new ModelEncryptDecorator(reverseEncryptDecorator);
        System.out.println(modelEncryptDecorator.encrypt("试试多次加密"));
    }
}
```

输出结果:

```
两次加密 + 简单加密 + 逆向输出加密
试试多次加密 + 简单加密 + 逆向输出加密 + 求模加密
```

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/decoratorPattern)
👉 [返回设计模式概览](../../设计模式概览)