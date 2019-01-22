---
title: 面试题-JAVA基本功
date: 2019-01-08 12:32:00
tags:
  - JAVA
  - 面试
categories: 
  - 理论学习
  - JAVA面试题通关
---
俗话说，温故而知新，可以为师矣。任何一项工作，都离不开扎实的基础。而扎实的基础，就需要不停的拿出来温习。<!-- more -->

# Q1.面向对象的特征

## 三大特征(EIP)

* 封装E：把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行信息隐藏。
* 继承I: 继承是指这样一种能力：它可以使用现有类的所有功能，并在无需重新编写原来的类的情况下对这些功能进行扩展。
* 多态P: 所谓多态就是指一个类实例的相同方法在不同情形有不同表现形式。多态机制使具有不同内部结构的对象可以共享相同的外部接口。

## 五大原则

* 单一职责原则（Single-Resposibility Principle） 一个类应该仅有一个引起它变化的原因
* 开放封闭原则（Open-Closed principle） 对扩展是开放的，对更改是封闭的！
* 里氏替换原则（Liskov-Substituion Principle） 子类可以替换父类并且出现在父类能够出现的任何地方,贯彻GOF倡导的面向接口编程
* 依赖倒置原则（Dependecy-Inversion Principle） 高层模块不依赖低层模块
* ISP 接口隔离原则(Interface-Segregation Principle) 使用多个专门的接口比使用单个接口要好的多！

# Q2.final, finally, finalize 的区别

* final 用于声明属性,方法和类, 分别表示属性不可变, 方法不可覆盖, 类不可继承.
* finally 是异常处理语句结构的一部分，表示总是执行.
* finalize 是Object类的一个方法，在垃圾收集器执行的时候会调用被回收对象的此方法，可以覆盖此方法提供垃圾收集时的其他资源回收，例如关闭文件等. JVM不保证此方法总被调用.

# Q3.int和Integer有什么区别

* Integer是int的包装类；int是基本数据类型；
* Integer变量必须实例化后才能使用；int变量不需要；
* Integer实际是对象的引用，指向此new的Integer对象；int是直接存储数据值 ；
* Integer的默认值是null；int的默认值是0。

# Q4.重载和重写的区别

|区别点|重载方法|重写方法|
|-|-|-|
|参数列表|必须修改|一定不能修改|
|返回类型|可以修改|一定不能修改|
|异常|可以修改|可以减少或删除，一定不能抛出新的或者更广的异常|
|访问|可以修改|一定不能做更严格的限制（可以降低限制|

# Q5.抽象类和接口有什么区别

|参数|抽象类|接口|
|-|-|
|默认的方法实现|它可以有默认的方法实现|接口完全是抽象的。它根本不存在方法的实现|
|实现|子类使用extends关键字来继承抽象类。如果子类不是抽象类的话，它需要提供抽象类中所有声明的方法的实现。|子类使用关键字implements来实现接口。它需要提供接口中所有声明的方法的实现|
|构造器|抽象类可以有构造器|接口不能有构造器|
|与正常Java类的区别|除了你不能实例化抽象类之外，它和普通Java类没有任何区别|接口是完全不同的类型|
|访问修饰符|抽象方法可以有public、protected和default这些修饰符|接口方法默认修饰符是public。你不可以使用其它修饰符。|
|main方法|抽象方法可以有main方法并且我们可以运行它|接口没有main方法，因此我们不能运行它。|
|多继承|抽象方法可以继承一个类和实现多个接口|接口只可以继承一个或多个其它接口|
|速度|它比接口速度要快|接口是稍微有点慢的，因为它需要时间去寻找在类中实现的方法。|
|添加新方法|如果你往抽象类中添加新的方法，你可以给它提供默认的实现。因此你不需要改变你现在的代码。|如果你往接口中添加方法，那么你必须改变实现该接口的类。|

# Q6.JAVA的反射机制

* 在运行时判断任意一个对象所属的类；
* 在运行时构造任意一个类的对象；
* 在运行时判断任意一个类所具有的成员变量和方法（通过反射甚至可以调用private方法）；
* 在运行时调用任意一个对象的方法

重点：**是运行时而不是编译时**

## 反射的一些注意事项
- 由于反射会额外消耗一定的系统资源，因此如果不需要动态地创建一个对象，那么就不需要用反射。
- 另外，反射调用方法时可以忽略权限检查，因此可能会破坏封装性而导致安全问题。

## 代码实践

### 1.获得 Class 对象
```java
//通过getClass方法
String s = "text";
Class<?> c = s.getClass();

//通过forName方法
Class<?> c1 = Class.forName("java.lang.String");

//使用.class
Class<String> c2 = String.class;
Class<Integer> type = Integer.TYPE;
```

### 2.判断是否为某个类的实例
```java
List<String> testList = new ArrayList<>();
System.out.println(ArrayList.class.isInstance(testList));
```

### 3.创建实例
```java
String str = c2.newInstance();
System.out.println("newInstance 创建实例 : " + str);
//------
Constructor<String> constructor = c2.getConstructor(String.class);
String test = constructor.newInstance("test");
System.out.println("constructor 创建实例 : " + test);
```

### 4.获取方法
```java
Class<MethodClass> clazz = MethodClass.class;
MethodClass methodClass = clazz.newInstance();
Method[] methods = clazz.getMethods();
Method[] declaredMethods = clazz.getDeclaredMethods();
//获取methodClass类的add方法
Method method = clazz.getMethod("add", int.class, int.class);
System.out.println(method.invoke(methodClass, 1, 2));
//getMethods()方法获取的所有方法
System.out.println("getMethods获取的方法：");
for (Method m : methods) {
    System.out.println(m);
}
//getDeclaredMethods()方法获取的所有方法
System.out.println("getDeclaredMethods获取的方法：");
for (Method m : declaredMethods) {
    System.out.println(m);
}
```

### 5.利用反射创建数组
```java
Class<?> cls = Class.forName("java.lang.String");
Object array = Array.newInstance(cls,25);
//往数组里添加内容
Array.set(array,0,"hello");
Array.set(array,1,"Java");
Array.set(array,2,"fuck");
Array.set(array,3,"Scala");
Array.set(array,4,"Clojure");
//获取某一项的内容
System.out.println(Array.get(array,3));
```

# Q7.说说自定义注解的场景及实现

## 元注解
- @Target: 作用：用于描述注解的使用范围（即：被描述的注解可以用在什么地方）

```java
取值(ElementType)有：

　　　　1.CONSTRUCTOR:用于描述构造器
　　　　2.FIELD:用于描述域
　　　　3.LOCAL_VARIABLE:用于描述局部变量
　　　　4.METHOD:用于描述方法
　　　　5.PACKAGE:用于描述包
　　　　6.PARAMETER:用于描述参数
　　　　7.TYPE:用于描述类、接口(包括注解类型) 或enum声明
```
- @Retention: 作用：表示需要在什么级别保存该注释信息，用于描述注解的生命周期（即：被描述的注解在什么范围内有效）

```java
取值（RetentionPoicy）有：

　　　　1.SOURCE:在源文件中有效（即源文件保留）
　　　　2.CLASS:在class文件中有效（即class保留）
　　　　3.RUNTIME:在运行时有效（即运行时保留）
```
- @Documented: 用于描述其它类型的annotation应该被作为被标注的程序成员的公共API，因此可以被例如javadoc此类的工具文档化。
- @Inherited: 如果一个使用了@Inherited修饰的annotation类型被用于一个class，则这个annotation将被用于该class的子类。

## 代码实践

### 自定义一个注解

```java
// 定义@NeedTest注解的保留期限，该注解会保存到目标类的字节码中，并且会被类加载器加载到JVM中
@Retention(RetentionPolicy.RUNTIME) 
// 定义@NeedTest注解的应用目标，这是一个方法级别的注解
@Target(ElementType.METHOD)
public @interface NeedTest {
    boolean value() default true; // 单个成员，成员名必须是value(), 默认值是true
}
```

### 创建一个类来使用该注解

```java
public class MyService {
    public void saySomething() {
        System.out.println("say something");
    }

    @NeedTest(true) // 成员名value()设置为true
    public void sayHello(String name) {
        System.out.println("hello " + name);
    }

    @NeedTest(false) // 成员名value()设置为false
    public void sayHi(String name) {
        System.out.println("hi " + name);
    }
}
```

### 创建测试类，获取注解的属性

```java
public class Test {
    public static void main(String[] args) {
        Class clazz = MyService.class;
        Method[] methods = clazz.getDeclaredMethods();
        if (methods.length == 0) {
            System.out.println("method " + clazz.getName() + " has no declared method");
        } else {
            for (Method method : methods) {
				// 所有自定义的注解都隐式继承自java.lang.annotation.Annotation接口，但是不允许显示继承其他接口
                NeedTest annotation = method.getAnnotation(NeedTest.class); 
                if (annotation == null) {
                    System.out.println("method" + method.getName() + " has not annotated @NeedTest");
                } else {
                    boolean value = annotation.value();
                    System.out.println(method.getName() + " has annotated @NeedTest and value = " + value);
                }
            }
        }
    }
}
```

# Q8.HTTP请求的GET与POST方式的区别

1. 最直观的就是语义上的区别，get用于获取数据，post用于提交数据。
2. get参数有长度限制（受限于url长度，具体的数值取决于浏览器和服务器的限制），而post无限制

# Q9.session与cookie区别

1. cookie数据存放在客户的浏览器上，session数据放在服务器上
2. cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗,如果主要考虑到安全应当使用session
3. session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，如果主要考虑到减轻服务器性能方面，应当使用cookie
4. 单个cookie在客户端的限制是3K，就是说一个站点在客户端存放的cookie不能超过3K。
5. 一般将登陆信息等重要信息存放为session;其他信息如果需要保留，可以放在cookie中

# Q10.session分布式处理

这个暂时没能理解，先记下参考地址：[https://my.oschina.net/u/1774673/blog/871912](https://my.oschina.net/u/1774673/blog/871912)

# Q11.JDBC流程

1. 加载Driver类，注册数据库驱动；

```java
Class.forName("com.sqlite.jdbc.Driver");
```

2. 通过DriverManager,使用url，用户名和密码建立连接(Connection)；

```java
connection = DriverManager.getConnection("jdbc:sqlite:C:\\db\\test.db");
```

3. 通过Connection，使用sql语句打开Statement对象；

```java
ps = connection.prepareStatement("select * from student where age =?");
ps.setInt(1, 10);
```

4. 执行语句，将结果返回resultSet；

```java
resultSet = ps.executeQuery();
```

5. 对结果resultSet进行处理；

```java
while (resultSet.next()) {
    String name = resultSet.getString("name");
    int age = resultSet.getInt("age");
    System.out.println("name : " + name + " ,age : " + age);
}
```

6. 倒叙释放资源resultSet -> preparedStatement -> connection。

```java
try {}
catch(){}
finally {
    if (resultSet != null) {
        try {
            resultSet.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    if (ps != null) {
        try {
            ps.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    if (connection != null) {
        try {
            connection.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

# Q12.MVC设计思想

MVC指的就是Model-View-Controller（模型－视图－控制器）,可以参考springmvc。

# Q13.equals与==的区别

## 1.基本数据类型，也称原始数据类型。

`byte,short,char,int,long,float,double,boolean`
他们之间的比较，应用双等号==,比较的是他们的值。

## 2.复合数据类型(类)
当他们用（==）进行比较的时候，比较的是他们在内存中的存放地址，所以，除非是同一个new出来的对象，他们的比较后的结果为true，否则比较后结果为false。