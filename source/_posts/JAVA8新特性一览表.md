---
title: JAVA8新特性一览表
date: 2019-01-09 20:16:58
tags: 
  - java
---

# 总览

- forEach() method in Iterable interface(Iterable接口中的forEach()方法)
- default and static methods in Interfaces(接口中的默认和静态方法)
- Functional Interfaces and Lambda Expressions(function接口和Lambda表达式)
- Java Stream API for Bulk Data Operations on Collections(用于集合上的批量数据操作的Java Stream API)
- Java Time API
- Collection API improvements
- Concurrency API improvements
- Java IO improvements

<!-- more -->

#  1.forEach() method in Iterable interface(Iterable接口中的forEach()方法)

每当我们需要遍历Collection时，我们需要创建一个Iterator，其目的是迭代，然后我们在循环中为Collection中的每个元素提供业务逻辑。如果没有正确使用迭代器，会抛出异常ConcurrentModificationException。

Java 8在java.lang.Iterable接口中引入了forEach方法，这样在编写代码时我们只关注业务逻辑。 forEach方法将java.util.function.Consumer对象作为参数，因此它有助于将我们的业务逻辑放在我们可以重用的单独位置。让我们通过简单的例子看看每个用法。

```
List<IntegermyList = new ArrayList<Integer>();
for(int i=0; i<10; i++) myList.add(i);

//使用iterator
Iterator<Integeriterator = myList.iterator();
while (iterator.hasNext()) {
    Integer next = iterator.next();
    System.out.println("Iterator Value::" + next);
}

//foreach + 匿名类
myList.forEach(new Consumer<Integer>() {

    public void accept(Integer t) {
        System.out.println("forEach anonymous class Value::"+t);
    }

});

//使用consumer 接口
MyConsumer action = new MyConsumer();
myList.forEach(action);

//使用lambda表达式
myList.forEach(System.out::println);
```

# 2.default and static methods in Interfaces(接口中的默认和静态方法)

jdk8之前，interface方法不能有实现，但是从Java 8开始，接口被增强为具有实现方法。我们可以使用default和static关键字来创建具有方法实现的接口。例如Iterable接口中的forEach方法实现是

```
default void forEach(Consumer<? super Taction) {
    Objects.requireNonNull(action);
    for (T t : this) {
        action.accept(t);
    }
}
```

## 示例代码

创建一个接口

```java
public interface MyInterface {

    void show();

    default void showA() {
        System.out.println("我是接口默认方法");
    }

    static void showB() {
        System.out.println("我是接口静态方法");
    }
}
```

创建该接口实现类

```java
public class MyClass implements MyInterface {
    @Override
    public void show() {
        System.out.println("我是实现方法");
    }

    //默认方法支持重写,不覆盖则执行接口的默认方法
    @Override
    public void showA() {
        System.out.println("我覆盖了接口的默认方法");
    }

    //静态方法不可以重写
}
```

测试

```java
public class Test {
    public static void main(String[] args) {
        MyClass myClass = new MyClass();
        myClass.show();
        myClass.showA();
        //通过类名.方法名调用接口静态方法
        MyInterface.showB();
    }
}
```

# 3.Functional Interfaces and Lambda Expressions（function接口和Lambda表达式）

如果你注意到上面的接口代码，你会注意到@FunctionalInterface注释。功能接口是Java 8中引入的新概念。**只有一个抽象方法的接口就变成了功能接口**。我们不需要使用@FunctionalInterface注释将接口标记为功能接口。 @FunctionalInterface注释是一种避免在功能界面中意外添加抽象方法的工具。您可以将其视为@Override注释，并且最佳实践是使用它。实例：java8 的runnable run接口，带有一个抽象方法:

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

功能接口的主要好处之一是可以使用lambda表达式来实例化它们。我们可以使用匿名类实例化一个接口，但代码看起来很笨重。

```
//使用匿名类实例化
Runnable runnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("My Runnable");
    }
};
```

由于功能接口只有一个方法，因此lambda表达式可以很容易地提供方法实现。我们只需要提供方法参数和业务逻辑。例如，我们可以使用lambda表达式将上面的实现编写为：

```
//使用lambda表达式
Runnable runnable1 = () -System.out.println("My Runnable");
runnable.run();
runnable1.run();
```

如果在方法实现中有单个语句，我们也不需要花括号。例如，上面的Interface1匿名类可以使用lambda实例化，如下所示：

```
Interface1 interface1 = (s) -System.out.println(s);
interface1.method1("interface1 method");
```

## lambda表达式扩展

### Java 中的 Lambda 表达式通常使用 (argument) -(body) 语法书写，例如：

```
(arg1, arg2...) -> { body }
(type1 arg1, type2 arg2...) -> { body }
```

### Lambda 表达式的结构

- 一个Lambda表达式可以有零个或多个参数
- 参数的类型既可以明确声明，也可以根据上下文来推断。例如：`(int a)`与`(a)`效果相同
- 所有参数需包含在圆括号内，参数之间用逗号相隔。例如：`(a, b)` 或 `(int a, int b)` 或 `(String a, int b, float c)`
- 空圆括号代表参数集为空。例如：`() -> 42`
- 当只有一个参数，且其类型可推导时，圆括号（）可省略。例如：`a -> return a * a`
- Lambda表达式的主体可包含零条或多条语句
- 如果Lambda表达式的主体只有一条语句，花括号{}可省略。匿名函数的返回类型与该主体表达式一致
- 如果Lambda表达式的主体包含一条以上语句，则表达式必须包含在花括号{}中（形成代码块）。匿名函数的返回类型与代码块的返回类型一致，若没有返回则为空

## 函数式接口扩展

函数式接口是只包含一个抽象方法声明的接口,可以使用`@FunctionalInterface`标记

### JDK8之前已有的函数式接口

- java.lang.Runnable
- java.util.concurrent.Callable
- java.security.PrivilegedAction
- java.util.Comparator
- java.io.FileFilter
- java.nio.file.PathMatcher
- java.lang.reflect.InvocationHandler
- java.beans.PropertyChangeListener
- java.awt.event.ActionListener
- javax.swing.event.ChangeListener

### 新定义的函数式接口

java.util.function中定义了几组类型的函数式接口以及针对基本数据类型的子接口。

- Predicate:传入一个参数，返回一个bool结果，方法为boolean test(T t)
- Consumer:传入一个参数，无返回值，纯消费。方法为void accept(T t)
- Function:传入一个参数，返回一个结果，方法为R apply(T t)
- Supplier:无参数传入，返回一个结果，方法为T get()
- UnaryOperator:一元操作符，继承Function,传入参数的类型和返回类型相同。
- BinaryOperator:二元操作符，传入的两个参数的类型和返回类型相同，继承BiFunction。

【示例】

```
Predicate<Integer> predicate = (i) -> i > 0;
Consumer<Integer> consumer = (i) -> System.out.println("consumer : " + i);
Function<Integer,Boolean> function = (i) -> i > 0;
Supplier<Integer> supplier = () -> 1;
UnaryOperator<Integer> unaryOperator = (i) -> i * i;
BinaryOperator<Integer> binaryOperator = (i1,i2) -> i1 * i2;

System.out.println(predicate.test(10));
consumer.accept(10);
System.out.println(function.apply(10));
System.out.println(supplier.get());
System.out.println(unaryOperator.apply(100));
System.out.println(binaryOperator.apply(100,200));
```

# 4.Java Stream API for Bulk Data Operations on Collections(用于集合上的批量数据操作的Java Stream API)

## 示例代码

```
//从 Collection 和数组
List<Integerlist = new ArrayList<>();
for(int i=0;i<100;i++) {
    list.add(i);
}
Stream<Integerstream = list.stream(); //串行流
Stream<Integerstream1 = list.parallelStream(); //并行流
Stream<Integerstream2 = Arrays.stream(list.toArray(new Integer[0]));
Stream<Integerstream3 = Stream.of(list.toArray(new Integer[0]));

//从 BufferedReader
BufferedReader bufferedReader = new BufferedReader(new FileReader(new File("path")));
Stream<Stringstream4 = bufferedReader.lines();

//静态工厂
IntStream stream5 = IntStream.rangeClosed(1, 100);//生成1-100 的int stream
Stream<Pathstream6 = Files.walk(Paths.get("path"), 100);

//自己构建 通过StreamSupport辅助类从spliterator产生流
Stream<Integerstream7 = StreamSupport.stream(list.spliterator(), false);

//其它
Random random = new Random();
IntStream stream8 = random.ints();

BitSet bitSet = BitSet.valueOf(new long[]{1L, 2L, 3L});
IntStream stream9 = bitSet.stream();

Pattern pattern = Pattern.compile("\\d+");
Stream<Stringstream10 = pattern.splitAsStream("111sda123sda");

JarFile jarFile = new JarFile("xxx.jar");
Stream<JarEntrystream11 = jarFile.stream();
```

# 5.Java Time API(Java时间API)

Java 8通过发布新的Date-Time API (JSR 310)来进一步加强对日期与时间的处理。对日期与时间的操作一直是Java程序员最痛苦的地方之一。标准的 java.util.Date以及后来的java.util.Calendar一点没有改善这种情况（可以这么说，它们一定程度上更加复杂）。

## Clock类

它通过指定一个时区，然后就可以获取到当前的时刻，日期与时间。Clock可以替换System.currentTimeMillis()与TimeZone.getDefault()。

```
// Get the system clock as UTC offset 
final Clock clock = Clock.systemUTC();
System.out.println(clock.instant());
System.out.println(clock.millis());
```

下面是程序在控制台上的输出:

```
2019-01-09T14:52:50.111Z
1547045570335
```

## LocaleDate与LocalTime

LocaleDate只持有ISO-8601格式且无时区信息的日期部分。相应的，LocaleTime只持有ISO-8601格式且无时区信息的时间部分。LocaleDate与LocalTime都可以从Clock中得到。

```
// Get the local date and local time
final LocalDate date = LocalDate.now();
final LocalDate dateFromClock = LocalDate.now(clock);

System.out.println(date);
System.out.println(dateFromClock);

// Get the local date and local time
final LocalTime time = LocalTime.now();
final LocalTime timeFromClock = LocalTime.now(clock);

System.out.println(time);
System.out.println(timeFromClock);
```

下面是程序在控制台上的输出：

```
2019-01-09
2019-01-09
22:52:50.383
14:52:50.383
```

## LocaleDateTime

LocaleDateTime把LocaleDate与LocaleTime的功能合并起来，它持有的是ISO-8601格式无时区信息的日期与时间。

```
// Get the local date/time
final LocalDateTime datetime = LocalDateTime.now();
final LocalDateTime datetimeFromClock = LocalDateTime.now(clock);

System.out.println(datetime);
System.out.println(datetimeFromClock);
```

下面是程序在控制台上的输出:

```
2019-01-09T22:55:05.194
2019-01-09T14:55:05.194
```

## ZonedDateTime

如果你需要特定时区的日期/时间，那么ZonedDateTime是你的选择。它持有ISO-8601格式具具有时区信息的日期与时间。

```
// Get the zoned date/time
final ZonedDateTime zonedDatetime = ZonedDateTime.now();
final ZonedDateTime zonedDatetimeFromClock = ZonedDateTime.now(clock);
final ZonedDateTime zonedDatetimeFromZone = ZonedDateTime.now(ZoneId.of("America/Los_Angeles"));

System.out.println(zonedDatetime);
System.out.println(zonedDatetimeFromClock);
System.out.println(zonedDatetimeFromZone);
```

下面是程序在控制台上的输出：

```
2019-01-09T22:56:34.033+08:00[Asia/Shanghai]
2019-01-09T14:56:34.033Z
2019-01-09T06:56:34.035-08:00[America/Los_Angeles]
```

## Duration

在秒与纳秒级别上的一段时间。Duration使计算两个日期间的不同变的十分简单。

```
// Get duration between two dates
final LocalDateTime from = LocalDateTime.of(2018, Month.APRIL, 16, 0, 0, 0);
final LocalDateTime to = LocalDateTime.of(2019, Month.APRIL, 16, 23, 59, 59);

final Duration duration = Duration.between(from, to);
System.out.println("Duration in days: " + duration.toDays());
System.out.println("Duration in hours: " + duration.toHours());
```

上面的例子计算了两个日期2018年4月16号与2019年4月16号之间的过程。下面是程序在控制台上的输出：

```
Duration in days: 365
Duration in hours: 8783
```

# Collection API improvements(集合API改进)

上面已经展示了forEach()方法和Stream API在集合上的使用。java8的Collection API中添加了一些新方法：

## Iterator default method forEachRemaining(Consumer action) 

为每个元素执行给定操作，直到所有元素都已处理或操作引发异常。

### 源码

```
default void forEachRemaining(Consumer<? super E> action) {
	//传入一个非空消费者
    Objects.requireNonNull(action);
	//遍历执行消费者函数
    while (hasNext())
        action.accept(next());
}
```

### 示例代码

```
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
Iterator<Integer> iterator = list.iterator();
//创建一个消费者
Consumer<Integer> consumer = i -> System.out.println("consumer print " + i);
//iterator的forEachRemaining将集合中的每个元素消费
iterator.forEachRemaining(consumer);
```

### 控制台输出

```
consumer print 1
consumer print 2
consumer print 3
...
```

## Collection default method removeIf(Predicate filter)

删除满足给定条件的此集合的所有元素。

### 源码

```
default boolean removeIf(Predicate<? super E> filter) {
	//传入一个非空谓语
    Objects.requireNonNull(filter);
    boolean removed = false;
    final Iterator<E> each = iterator();
    while (each.hasNext()) {
		//遍历元素，执行谓语的校验，如果为真，则删除该元素
        if (filter.test(each.next())) {
            each.remove();
            removed = true;
        }
    }
    return removed;
}
```

### 示例代码

```
List<Integer> list = new ArrayList<>();
list.add(1);
list.add(2);
list.add(3);
list.add(4);
Predicate<Integer> predicate = i -> i > 1;
list.removeIf(predicate);
System.out.println("remove if left items : " + list);
```

### 控制台输出

```
//2,3,4满足条件被删除了
remove if left items : [1]
```

## Collection spliterator()

返回Spliterator实例的方法，该实例可用于顺序或并行遍历元素。

### 源码

```
//该方法是接口默认方法
default Spliterator<E> spliterator() {
    return Spliterators.spliterator(this, Spliterator.ORDERED);
}
```

### 示例代码

```
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
Spliterator<Integer> spliterator = list.spliterator();
//创建顺序流
Stream<Integer> stream = StreamSupport.stream(spliterator, false);
//创建并行流
Stream<Integer> parallelStream = StreamSupport.stream(spliterator, true);
```

## Map replaceAll(), compute(), merge() methods

### replaceAll()

替换Map中所有Entry的value值，这个值由旧的key和value计算得出，接收参数 (K, V) -> V

#### 源码

```
public void replaceAll(BiFunction<? super K, ? super V, ? extends V> function) {
    Node<K,V>[] tab;
    if (function == null)
        throw new NullPointerException();
    if (size > 0 && (tab = table) != null) {
        int mc = modCount;
        for (int i = 0; i < tab.length; ++i) {
            for (Node<K,V> e = tab[i]; e != null; e = e.next) {
				//使用给定的函数替换原来的value值，key不变
                e.value = function.apply(e.key, e.value);
            }
        }
        if (modCount != mc)
            throw new ConcurrentModificationException();
    }
}
```

#### 示例代码

```
Map<String, String> map = new HashMap<>();
map.put("1", "A");
map.put("2", "B");
map.put("3", "C");
map.put("4", "D");
map.put("5", "E");
//replaceAll方法
map.replaceAll((s, s2) -> s + s2);
System.out.println(map);
```

#### 控制台输出

```
//原来的value由key + value替换掉了
{1=1A, 2=2B, 3=3C, 4=4D, 5=5E}
```

### compute()

是`computeIfPresent`和`computeIfAbsent`方法的组合体

- computeIfPresent:如果指定的key不存在，则通过指定的K -> V计算出新的值设置为key的值。
- computeIfPresent:如果指定的key存在，则根据旧的key和value计算新的值newValue, 如果newValue不为null，则设置key新的值为newValue, 如果newValue为null, 则删除该key的值。

#### 示例代码

```
Map<String, String> map = new HashMap<>();
map.put("1", "A");
map.put("2", "B");
map.put("3", "C");
map.put("4", "D");
map.put("5", "E");
//key存在，根据旧的key和value计算新的值newValue
map.compute("1", (k, v) -> v + " computed");
System.out.println("key存在" + map.get("1"));
//key不存在，通过指定的K -> V计算出新的值设置为key的值
map.compute("6", (k, v) -> "F");
System.out.println("key不存在" + map.get("6"));
//key存在，如果newValue为null, 则删除该key的值
map.compute("1", (k, v) -> null);
System.out.println("key存在，设置为null " + map.get("1"));
```

#### 控制台输出

```
key存在A computed
key不存在F
key存在，设置为null null
```

### merge()

如果指定的key不存在，则设置指定的value值，否则根据key的旧的值oldvalue，value计算出新的值newValue, 如果newValue为null, 则删除该key，否则设置key的新值newValue。

#### 示例代码

```
Map<String, String> map = new HashMap<>();
map.put("1", "A");
map.put("2", "B");
map.put("3", "C");
map.put("4", "D");
map.put("5", "E");
//存在key为1,输出 Amerge
System.out.println(map.merge("1", "merge", (k, v) -> k + v));
//新值为null，删除key，输出 null
System.out.println(map.merge("1", "merge", (k, v) -> null));
//不存在key为6，输出 "merge"
System.out.println(map.merge("6", "merge", (k, v) -> k + v));
```

#### 控制台输出

```
Amerge
null
merge
```
## Performance Improvement for HashMap class with Key Collisions

具有键冲突的HashMap类的性能改进

# Concurrency API improvements(并发API改进)

## ConcurrentHashMap

JDK8提供的并发友好的HashMap

## CompletableFuture

提供了非常强大的 Future 的扩展功能，可以帮助我们简化异步编程的复杂性，并且提供了函数式编程的能力，可以通过回调的方式处理计算结果，也提供了转换和组合 CompletableFuture 的方法。

## Executors newWorkStealingPool()

创建持有足够线程的线程池来支持给定的并行级别，并通过使用多个队列，减少竞争，它需要传一个并行级别的参数，如果不传，则被设定为默认的CPU数量。

# Java IO improvements(Java IO API的改进)

## Files.list(Path dir)

返回一个延迟填充的Stream，其中的元素是目录中的条目。

```
//返回目录下的元素集合流
Stream<Path> list = Files.list(new File("C:\\Users\\Administrator\\Desktop").toPath());
list.forEach(System.out::println);
```

## Files.lines(Path path)

从文件中读取所有行作为流。

```
//返回文件中的所有行数
Stream<String> lines = Files.lines(new File("C:\\Users\\Administrator\\Desktop\\new 3.txt").toPath());
lines.forEach(System.out::println);
```

## Files.find()

通过搜索以给定起始文件为根的文件树中的文件，返回使用Path延迟填充的Stream。

```
//返回符合判断条件的Path流
Stream<Path> stream = Files.find(new File("C:\\Users\\Administrator\\Desktop").toPath(),
        1,
        (path, basicFileAttributes) -> basicFileAttributes.isDirectory());
stream.forEach(System.out::println);
```

## BufferedReader.lines()

返回一个Stream，其元素是从这个BufferedReader读取的行。

```
//返回文件中的所有行数,类似Files.lines()
BufferedReader br = new BufferedReader(new FileReader("C:\\Users\\Administrator\\Desktop\\new 3.txt"));
Stream<String> stringStream = br.lines();
stringStream.forEach(System.out::println);
```

# 参考资源

- [Java 8 Features with Examples](https://www.journaldev.com/2389/java-8-features-with-examples#java8-collection)
- [为并发而生的 ConcurrentHashMap（Java 8）](https://www.cnblogs.com/yangming1996/p/8031199.html)
- [通过实例理解 JDK8 的 CompletableFuture](https://www.ibm.com/developerworks/cn/java/j-cf-of-jdk8/index.html)