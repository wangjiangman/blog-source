title: "Java Lambda 表达式学习笔记"
date: 2016-02-24
tags: [Java, Lambda]
---

Java Lambda 表达式是 Java 8 引入的一个新的功能，可以说是模拟函数式编程的一个语法糖，类似于 Javascript 中的闭包，但又有些不同，主要目的是提供一个函数化的语法来简化我们的编码。<!--more-->

## Lambda 基本语法

Lambda 的基本结构为 <code>(arguments) -> body</code>，有如下几种情况：

- 参数类型可推导时，不需要指定类型，如 <code>(a) -> System.out.println(a)</code>
- 当只有一个参数且类型可推导时，不强制写 <code>()</code>, 如 <code>a -> System.out.println(a)</code>
- 参数指定类型时，必须有括号，如 <code>(int a) -> System.out.println(a)</code>
- 参数可以为空，如 <code>() -> System.out.println("hello")</code>
- body 需要用 <code>{}</code> 包含语句，当只有一条语句时 <code>{}</code> 可省略

常见的写法如下：

```
(a) -> a * a
(int a, int b) -> a + b
(a, b) -> {return a - b;}
() -> System.out.println(Thread.currentThread().getId())
```

## 函数式接口 FunctionalInterface

### 概念

Java Lambda 表达式以函数式接口为基础。什么是函数式接口（FunctionalInterface）？ 简单说来就是只有一个方法（函数）的接口，这类接口的目的是为了一个单一的操作，也就相当于一个单一的函数了。常见的接口如：Runnable, Comparator 都是函数式接口，并且都标注了注解 <code>@FunctionalInterface</code> 。

### 举例

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

当然 Lambda 的目的不仅仅是写起来简洁，更高层次的目的等体会到了再总结。

再看一个比较器的例子，按照传统的写法，如下：

```java
Integer[] a = {1, 8, 3, 9, 2, 0, 5};
Arrays.sort(a, new Comparator<Integer>() {
	@Override
	public int compare(Integer o1, Integer o2) {
		return o1 - o2;
	}
});
```

Lambda 表达式写法如下：

```java
Integer[] a = {1, 8, 3, 9, 2, 0, 5};
Arrays.sort(a, (o1, o2) -> o1 - o2);
```

### JDK中的函数式接口

为了现有的类库能够直接使用 Lambda 表达式，Java 8 以前存在一些接口已经被标注为函数式接口的：

- <code>java.lang.Runnable</code>
- <code>java.util.Comparator</code>
- <code>java.util.concurrent.Callable</code>
- <code>java.io.FileFilter</code>
- <code>java.security.PrivilegedAction</code>
- <code>java.beans.PropertyChangeListener</code>

Java 8 中更是新增加了一个包 <code>java.util.function</code>，带来了常用的函数式接口：

- <code>Function&lt;T, R></code> - 函数：输入 T 输出 R
- <code>BiFunction&lt;T, U, R></code> - 函数：输入 T 和 U 输出 R 对象
- <code>Predicate&lt;T></code> - 断言/判断：输入 T 输出 boolean
- <code>BiPredicate&lt;T, U></code> - 断言/判断：输入 T 和 U 输出 boolean 
- <code>Supplier&lt;T></code> - 生产者：无输入，输出 T
- <code>Consumer&lt;T></code> - 消费者：输入 T，无输出
- <code>BiConsumer&lt;T, U></code> - 消费者：输入 T 和 U 无输出
- <code>UnaryOperator&lt;T></code> - 单元运算：输入 T 输出 T
- <code>BinaryOperator&lt;T></code> - 二元运算：输入 T 和 T 输出 T

另外还对基本类型的处理增加了更加具体的函数是接口，包括：<code>BooleanSupplier</code>, <code>DoubleBinaryOperator</code>, <code>DoubleConsumer</code>, <code>DoubleFunction&lt;R></code>, <code>DoublePredicate</code>, <code>DoubleSupplier</code>, <code>DoubleToIntFunction</code>, <code>DoubleToLongFunction</code>, <code>DoubleUnaryOperator</code>, <code>IntBinaryOperator</code>, <code>IntConsumer</code>, <code>IntFunction&lt;R></code>, <code>IntPredicate</code>, <code>IntSupplier</code>, <code>IntToDoubleFunction</code>, <code>IntToLongFunction</code>, <code>IntUnaryOperator</code>, <code>LongBinaryOperator</code>, <code>LongConsumer</code>, <code>LongFunction&lt;R></code>, <code>LongPredicate</code>, <code>LongSupplier</code>, <code>LongToDoubleFunction</code>, <code>LongToIntFunction</code>, <code>LongUnaryOperator</code>, <code>ToDoubleBiFunction&lt;T, U></code>, <code>ToDoubleFunction&lt;T></code>, <code>ToIntBiFunction&lt;T, U></code>, <code>ToIntFunction&lt;T></code>, <code>ToLongBiFunction&lt;T, U></code>, <code>ToLongFunction&lt;T></code> 。结合上面的函数式接口，对这些基本类型的函数式接口通过类名就能一眼看出接口的作用。

### 创建函数式接口

有时候我们需要自己实现一个函数式接口，做法也很简单，首先你要保证此接口只能有一个函数操作，然后在接口类型上标注注解 <code>@FunctionalInterface</code> 即可。

## 类型推导

类型推导是 Lambda 表达式的基础，类型推导的过程就是 Lambda 表达式的编译过程。以下面的代码为例：

```java
Function<String, Integer> strToInt = str -> Integer.parseInt(str);
```

编译期间，我理解的类型推导的过程如下：

1. 先确定目标类型 Function<String, Integer>
1. Function<String, Integer> 作为函数式接口，其方法签名为：Integer apply(String t)
1. 检测 str -> Integer.parseInt(str) 是否与方法签名匹配（方法的参数类型、个数、顺序 和返回值类型）
1. 如果不匹配，则报编译错误

这里的目标类型是关键，通过目标类型获取方法签名，然后和 Lambda 表达式做出对比。

## 方法引用

方法引用(Method Reference)的基础同样是函数式接口，可以直接作为函数式接口的实现，与 Lambda 表达式有相同的作用，同样依赖于类型推导。方法引用可以看作是只调用一个方法的 Lambda 表达式的简化。

方法引用的语法为： <code>Type::methodName</code> 或者 <code>instanceName::methodName</code> , 构造函数对应的 methodName 为 new。

例如上面曾用到例子：

```java
Function<String, Integer> strToInt = str -> Integer.parseInt(str);
```

对应的方法引用的写法为

```java
Function<String, Integer> strToInt = Integer::parseInt;
```

根据方法的类型，方法引用主要分为一下几种类型，构造方法引用、静态方法引用、实例上实例方法引用、类型上实例方法引用等

### 构造方法引用

语法为： <code>Type::new</code> 。 如下面的函数为了将字符串转为数组

**方法引用写法**

```java
Function<String, Integer> strToInt = Integer::new;
```

**Lambda 写法**

```java
Function<String, Integer> strToInt = str -> new Integer(str);
```

**传统写法** 

```java
Function<String, Integer> strToInt = new Function<String, Integer>() {
	@Override
	public Integer apply(String str) {
		return new Integer(str);
	}
};
```

### 数组构造方法引用

语法为： <code>Type[]::new</code> 。如下面的函数为了构造一个指定长度的字符串数组

**方法引用写法**

```java
Function<Integer, String[]> fixedArray = String[]::new;
```

**方法引用写法**

```java
Function<Integer, String[]> fixedArray = length -> new String[length];
```

**传统写法** 

```java
Function<Integer, String[]> fixedArray = new Function<Integer, String[]>() {
	@Override
	public String[] apply(Integer length) {
		return new String[length];
	}
};
```

### 静态方法引用

语法为： <code>Type::new</code> 。 如下面的函数同样为了将字符串转为数组

**方法引用写法**

```java
Function<String, Integer> strToInt = Integer::parseInt;
```

**Lambda 写法**

```java
Function<String, Integer> strToInt = str -> Integer.parseInt(str);
```

**传统写法** 

```java
Function<String, Integer> strToInt = new Function<String, Integer>() {
	@Override
	public Integer apply(String str) {
		return Integer.parseInt(str);
	}
};
```

### 实例上实例方法引用

语法为： <code>instanceName::methodName</code> 。如下面的判断函数用来判断给定的姓名是否在列表中存在

```java
List<String> names = Arrays.asList(new String[]{"张三", "李四", "王五"});
Predicate<String> checkNameExists = names::contains;
System.out.println(checkNameExists.test("张三"));
System.out.println(checkNameExists.test("张四"));
```

### 类型上实例方法引用

语法为： <code>Type::methodName</code> 。运行时引用是指上下文中的对象，如下面的函数来返回字符串的长度

```java
Function<String, Integer> calcStrLength = String::length;
System.out.println(calcStrLength.apply("张三"));

List<String> names = Arrays.asList(new String[]{"zhangsan", "lisi", "wangwu"});
names.stream().map(String::length).forEach(System.out::println);
```

又比如下面的函数已指定的分隔符分割字符串为数组

```java
BiFunction<String, String, String[]> split = String::split;
String[] names = split.apply("zhangsan,lisi,wangwu", ",");
System.out.println(Arrays.toString(names));
```

## Stream 对象

### 概念

什么是 Stream ? 这里的 Stream 不同于 io 中的 InputStream 和 OutputStream，Stream 位于包 java.util.stream 中， 也是 java 8 新加入的，Stream 只的是一组支持串行并行聚合操作的元素，可以理解为集合或者迭代器的增强版。什么是聚合操作？简单举例来说常见的有平均值、最大值、最小值、总和、排序、过滤等。

Stream 的几个特征：

- 单次处理。一次处理结束后，当前Stream就关闭了。
- 支持并行操作

### 常见的获取 Stream 的方式

- 从集合中获取
  - Collection.stream();
  - Collection.parallelStream();
- 静态工厂
  - Arrays.stream(array) 
  - Stream.of(T ...)
  - IntStream.range()

这里只对 Stream 做简单的介绍，下面会有具体的应用。要说 Stream 与 Lambda 表达式有什么关系，其实并没有什么特别紧密的关系，只是 Lambda 表达式极大的方便了 Stream 的使用。如果没有 Lambda 表达式，使用 Stream 的过程中会产生大量的匿名类，非常别扭。


## 举例

以下的demo依赖于 Employee 对象，以及由 Employee 对象组成的 List 对象。

```java
public class Employee {
	
	private String name;
	private String sex;
	private int age;
	
	public Employee(String name, String sex, int age) {
		super();
		this.name = name;
		this.sex = sex;
		this.age = age;
	}

	public String getName() {
		return name;
	}
	
	public String getSex() {
		return sex;
	}

	public int getAge() {
		return age;
	}

	@Override
	public String toString() {
		StringBuilder builder = new StringBuilder();
		builder.append("Employee {name=").append(name).append(", sex=").append(sex).append(", age=").append(age)
				.append("}");
		return builder.toString();
	}	
}
```

``` java
List<Employee> employees = new ArrayList<>();
employees.add(new Employee("张三", "男", 25));
employees.add(new Employee("李四", "女", 24));
employees.add(new Employee("王五", "女", 23));
employees.add(new Employee("周六", "男", 22));
employees.add(new Employee("孙七", "女", 21));
employees.add(new Employee("刘八", "男", 20));
```

### 打印所有员工

Collection 提供了 forEach 方法，供我们逐个操作单个对象。

```java
employees.forEach(e -> System.out.println(e)); 
或者
employees.stream().forEach(e -> System.out.println(e)); 
```

### 按年龄排序

```java
Collections.sort(employees, (e1, e2) -> e1.getAge() - e2.getAge());
employees.forEach(e -> System.out.println(e));
或者
employees.stream().sorted((e1, e2) -> e1.getAge() - e2.getAge()).forEach(e -> System.out.println(e)); 
```

### 打印年龄最大的女员工

max/min 返回指定排序条件下最大/最小的元素

```java
Employee maxAgeFemaleEmployee = employees.stream()
		.filter(e -> "女".equals(e.getSex()))
		.max((e1, e2) -> e1.getAge() - e2.getAge())
		.get();
System.out.println(maxAgeFemaleEmployee);
```

### 打印出年龄大于20 的男员工

filter 可以过滤出符合条件的元素

```java
employees.stream()
		.filter(e -> e.getAge() > 20 && "男".equals(e.getSex()))
		.forEach(e -> System.out.println(e));
```

### 打印出年龄最大的2名男员工

limit 方法截取有限的元素

```java
employees.stream()
		.filter(e -> "男".equals(e.getSex()))
		.sorted((e1, e2) -> e2.getAge() - e1.getAge())
		.limit(2)
		.forEach(e -> System.out.println(e));
```

### 打印出所有男员工的姓名，使用 , 分隔

map 将 Stream 中所有元素的执行给定的函数后返回值组成新的 Stream

```java
String maleEmployeesNames = employees.stream()
		.map(e -> e.getName())
		.collect(Collectors.joining(","));
System.out.println(maleEmployeesNames);
```

### 统计信息

IntSummaryStatistics, DoubleSummaryStatistics, LongSummaryStatistics 包含了 Stream 中的汇总数据。

```java
IntSummaryStatistics stat = employees.stream()
		.mapToInt(Employee::getAge).summaryStatistics();
System.out.println("员工总数：" + stat.getCount());
System.out.println("最高年龄：" + stat.getMax());
System.out.println("最小年龄：" + stat.getMin());
System.out.println("平均年龄：" + stat.getAverage());
```

## 总结

Lambda 表达式确实可以减少很多代码，能提高生产力，当然也有弊端，就是复杂的表达式可读性会比较差，也可能是还不是很习惯的缘故吧，如果习惯了，相信会喜欢上的。凡事都有两面性，就看我们如何去平衡这其中的利弊了，尤其是在一个团队中。