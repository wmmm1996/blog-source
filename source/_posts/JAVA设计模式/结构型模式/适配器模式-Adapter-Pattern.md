---
title: 适配器模式-Adapter Pattern
date: 2019-01-15 17:03:38
tags:
  - java
  - 设计模式
categories: 
  - JAVA设计模式
---

# 概述
> 将一个接口转换成客户希望的另一个接口，使接口不兼容的那些类可以一起工作，其别名为包装器(Wrapper)。

<!-- more -->

# 适配器分类

## 默认适配器模式(Default Adapter)

我们用 Appache commons-io 包中的 FileAlterationListener 做例子，此接口定义了很多的方法，用于对文件或文件夹进行监控，一旦发生了对应的操作，就会触发相应的方法。

```java
public interface FileAlterationListener {
    void onStart(final FileAlterationObserver observer);
    void onDirectoryCreate(final File directory);
    void onDirectoryChange(final File directory);
    void onDirectoryDelete(final File directory);
    void onFileCreate(final File file);
    void onFileChange(final File file);
    void onFileDelete(final File file);
    void onStop(final FileAlterationObserver observer);
}
```

此接口的一大问题是抽象方法太多了，如果我们要用这个接口，意味着我们要实现每一个抽象方法，如果我们只是想要监控文件夹中的文件创建和文件删除事件，可是我们还是不得不实现所有的方法，很明显，这不是我们想要的。

所以，我们需要下面的一个适配器，它用于实现上面的接口，但是所有的方法都是空方法，这样，我们就可以转而定义自己的类来继承下面这个类即可。

```java
public class FileAlterationListenerAdaptor implements FileAlterationListener {

    public void onStart(final FileAlterationObserver observer) {
    }

    public void onDirectoryCreate(final File directory) {
    }

    public void onDirectoryChange(final File directory) {
    }

    public void onDirectoryDelete(final File directory) {
    }

    public void onFileCreate(final File file) {
    }

    public void onFileChange(final File file) {
    }

    public void onFileDelete(final File file) {
    }

    public void onStop(final FileAlterationObserver observer) {
    }
}
```

比如我们可以定义以下类，我们仅仅需要实现我们想实现的方法就可以了：

```java
public class FileMonitor extends FileAlterationListenerAdaptor {
    public void onFileCreate(final File file) {
        // 文件创建
        doSomething();
    }

    public void onFileDelete(final File file) {
        // 文件删除
        doSomething();
    }
}
```

## 类适配器模式

### 组成

- 目标(Target)角色
> 这就是所期待得到的接口。注意：由于这里讨论的是类适配器模式，因此目标不可以是类。
- 源(Adapee)角色
> 现在需要适配的接口。
- 适配器(Adaper)角色
> 适配器类是本模式的核心。适配器把源接口转换成目标接口。显然，这一角色不可以是接口，而必须是具体类。

### 案例

UML类图

![](https://i.imgur.com/9iriQ74.png)

```java
//目标角色
public interface Target {
    /**
     * 这是源类Adaptee也有的方法
     */
    void sampleOperation1();

    /**
     * 这是源类Adapteee没有的方法
     */
    void sampleOperation2();
}
//我们适配的角色,它有一个sampleOperation1()方法，但是没有sampleOperation2()方法
public class Adaptee {
    public void sampleOperation1() {
    }
}
//适配器角色Adapter扩展了Adaptee,同时又实现了目标(Target)接口。由于Adaptee没有提供sampleOperation2()方法，而目标接口又要求这个方法，因此适配器角色Adapter实现了这个方法。
public class Adapter extends Adaptee implements Target {
    /**
     * 由于源类Adaptee没有方法sampleOperation2()
     * 因此适配器补充上这个方法
     */
    @Override
    public void sampleOperation2() {
        //写相关的代码
    }
}
```

## 对象适配器模式

> 与类的适配器模式一样，对象的适配器模式把被适配的类的API转换成为目标类的API，与类的适配器模式不同的是，对象的适配器模式不是使用继承关系连接到Adaptee类，而是使用委派关系连接到Adaptee类。

### 案例

只有适配器不同，目标和源角色与类适配器一致，看看对象适配器模式下是怎么将源角色与目标适配起来的。

```java
public class Adapter {
    private Adaptee adaptee;

    public Adapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }

    /**
     * 源类Adaptee有方法sampleOperation1
     * 因此适配器类直接委派即可
     */
    public void sampleOperation1() {
        this.adaptee.sampleOperation1();
    }

    /**
     * 源类Adaptee没有方法sampleOperation2
     * 因此由适配器类需要补充此方法
     */
    public void sampleOperation2() {
        //写相关的代码
    }
}
```

# 优缺点

## 优点

- 更好的复用性
> 系统需要使用现有的类，而此类的接口不符合系统的需要。那么通过适配器模式就可以让这些功能得到更好的复用。
- 更好的扩展性
> 在实现适配器功能的时候，可以调用自己开发的功能，从而自然地扩展系统的功能。

## 缺点

- 过多的使用适配器，会让系统非常零乱，不易整体进行把握
> 比如，明明看到调用的是A接口，其实内部被适配成了B接口的实现，一个系统如果太多出现这种情况，无异于一场灾难。因此如果不是很有必要，可以不使用适配器，而是直接对系统进行重构。

# 练习
> Sunny软件公司OA系统需要提供一个加密模块，将用户机密信息（如口令、邮箱等）加密之后再存储在数据库中，系统已经定义好了数据库操作类。为了提高开发效率，现需要重用已有的加密算法，这些算法封装在一些由第三方提供的类中，有些甚至没有源代码。试使用适配器模式设计该加密模块，实现在不修改现有类的基础上重用第三方加密方法。

根据要求，模拟两个系统中已经存在的需要适配的类

```java
//已有的算法模块
public class EncryptService {
    public String encrypt(String s) {
        System.out.println("调用了加密算法");
        return "调用了加密算法" + s;
    }
}
//已有的数据库操作类
public class CRUDService {
    public void save(String s) {
        System.out.println("保存:" + s);
    }
}
```

开始适配:

```java
//目标类
public interface Target {
    /**
     * 原来已有的方法
     */
    void save(String s);

    /**
     * 新增的加密保存方法
     */
    void encryptSave(String s);
}
public class CRUDAdapter extends CRUDService implements Target {

    @Override
    public void encryptSave(String s) {
        EncryptService encryptService = new EncryptService();
        String encrypt = encryptService.encrypt(s);
        this.save(encrypt);
    }
}
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        CRUDAdapter crudAdapter = new CRUDAdapter();
        crudAdapter.save("用户1");
        crudAdapter.encryptSave("用户2");
    }
}
```

输出结果:

```
保存:用户1
调用了加密算法
保存:调用了加密算法用户2
```

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/adapterPattern)
👉 [返回设计模式概览](../../设计模式概览)