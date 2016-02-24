# Java Lambda 表达式学习笔记

Java Lambda 表达式时 Java 8 引入的一个新的功能，可以说是模拟函数式编程的一个语法糖，类似于 Javascript 中的闭包，但又有些不同，主要目的是提供一个函数化的语法来简化我们的编码。<!--more-->

## Lambda 基本语法

Lambda 的基本结构为 <code>(arguments) -> body</code>，有如下几种情况：

- 参数类型可推导时，不需要指定类型，如 <code>(a) -> System.out.println(a)</code>
- 当只有一个参数且类型可推导时，不强制写 <code>()</code>, 如 <code>a -> System.out.println(a)</code>
- 参数指定类型时，必须有括号，如 <code>(int a) -> System.out.println(a)</code>
- 参数可以为空，如 <code>() -> System.out.println("hello")</code>
- body 需要用 <code>{}</code> 包含语句，当只有一条语句时 <code>{}</code> 可省略

## 函数式接口 FunctionalInterface

Java Lambda 表达式以函数式接口为基础。什么是函数式接口（FunctionalInterface）？ 简单说来就是只有一个方法（函数）的接口，这类接口的目的是为了一个单一的操作，也就相当于一个单一的函数了。常见的接口如：Runnable, Comparator 都是函数式接口，并且都标注了注解 @FunctionalInterface 。

以 Thread 为例说明很容易理解。Runnable 接口是我们线程编程时常用的一个接口，就包含一个方法 <code>void run()</code>，这个方法就是线程的运行逻辑。按照以前的语法，我们新建线程一般要用到 Runnable 的匿名类，如下：

```java
new Thread(new Runnable() {

	@Override
	public void run() {
		System.out.println(Thread.currentThread().getId());
	}
	
}).start();
```

如果写多了，是不是很无聊，而基于 Lambda 的写法则变得简洁明了，如下：

```java
new Thread(() -> System.out.println(Thread.currentThread().getId())).start();
```

注意 Thread 的参数，Runnable 的匿名实现就通过一句就实现了出来，写成下面的更好理解

```java
Runnable r = () -> System.out.println(Thread.currentThread().getId());
new Thread(r).start();
```

除了 Runnable, Comparator ，其他的函数式接口还有：Consumer, Predicate 等。

有时候我们需要自己实现一个函数式接口，做法也很简单，首先你要保证此接口只能有一个函数操作，然后在接口类型上标注注解 @FunctionalInterface 即可。

## Stream 对象

什么是 Stream ? 这里的 Stream 不同于 io 中的 InputStream 和 OutputStream，Stream 位于包 java.util.stream 中， 也是 java 8 新加入的，Stream 只的是一组支持串行并行聚合操作的元素，可以理解为集合或者迭代器的增强版。什么时聚合操作？简单举例来说常见的有平均值、最大值、最小值、总和、排序、过滤等。

Stream 的几个特征：

- 单向迭代，迭代一次后数据就用尽了
- 支持并行操作

常见的获取 Stream 的方式

- 从集合中获取
  - Collection.stream();
  - Collection.parallelStream();
- 静态工厂
  - Arrays.stream(array) 
  - Stream.of(T ...)
  - IntStream.range()

这里只是个简单的介绍，下面会有具体的应用。

## 举例

## 参考资料

- https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/
