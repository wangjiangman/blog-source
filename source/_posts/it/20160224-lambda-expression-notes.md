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

### 常见的获取 Stream 的方式

- 从集合中获取
  - Collection.stream();
  - Collection.parallelStream();
- 静态工厂
  - Arrays.stream(array) 
  - Stream.of(T ...)
  - IntStream.range()

这里只是个简单的介绍，下面会有具体的应用。

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

Lambda 表达式确实可以减少很多代码，能提高生产力，当然也有弊端，就是复杂的表达式可读性会比较差，也可能是还不是很习惯的缘故吧。

## 参考资料

- https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/

