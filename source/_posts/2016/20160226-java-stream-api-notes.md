title: "Java Stream 学习笔记"
date: 2016-02-26
tags: [Java, Lambda]
categories: 技术
---

什么是 Stream ? 这里的 Stream 不同于 io 中的 InputStream 和 OutputStream，Stream 位于包 java.util.stream 中， 也是 java 8 新加入的，Stream 只的是一组支持串行并行聚合操作的元素，可以理解为集合或者迭代器的增强版。<!--more-->

Stream 支持操作主要有 map, reduce, 排序，最大值，最小值等。

Stream 的几个特征：

- 单次处理。一次处理结束后，当前Stream就关闭了。
- 支持并行操作

常见的获取 Stream 的方式

- 从集合中获取
  - Collection.stream();
  - Collection.parallelStream();
- 静态工厂
  - Arrays.stream(array) 
  - Stream.of(T ...)
  - IntStream.range()

## Optional 类

先说一下 Optional 类， 因为 Stream 中有些操作返回的 Optional 类，而不是对象本身。Optional 类始于 jdk 1.8，Optional 是对具体对象的简单的包装，并提供了部分操作 主要目的是防止产生空指针异常。如果以前了解过 Null Object Pattern，就很容易了解 Optional 存在的意义。

主要方法：

- get 返回包装的对象
- isPresent 判断是否包装了对象
- ifPresent(Consumer) 如果存在包装对象，则对包装对象进行一定的操作

## Stream 常用操作

### allMatch

使用给定的 Predicate 检查 Stream 中的所有元素，全部都通过检测则返回 true，否则 false 。

```java
System.out.println(Stream.of(1,2,3).allMatch(n -> n >= 1));
System.out.println(Stream.of(1,2,3).allMatch(n -> n >= 3));
System.out.println(Stream.of(1,2,3).allMatch(n -> n >= 4));
```

### anyMatch

使用给定的 Predicate 检查 Stream 中的所有元素，至少有一个通过检测则返回 true，否则 false 。

### collect

collect 操作使用给定的 Collector 做 reduce 操作。

**数组元素连接**

```java
System.out.println(Stream.of("A", "B", "C").collect(Collectors.joining(",")));
```

**转成 List**

```java
List<String> asList = Stream.of("A", "B", "C").collect(Collectors.toList());
```

**根据城市分组**

```java
Map<String, List<Person>> peopleByCity = personStream.collect(Collectors.groupingBy(Person::getCity));
```

** 根据州和城市分组**

```java
Map<String, Map<String, List<Person>>> peopleByStateAndCity
         = personStream.collect(Collectors.groupingBy(Person::getState,
                                                      Collectors.groupingBy(Person::getCity)));

```

### count

返回 Stream 中的元素总数。

```java
System.out.println(Stream.of(1,2,3).count());
```

### distinct

返回唯一的元素列表，类似于 数据库 sql 中的 distinct 关键字。 比较时通过 equals 方法来判定是否相同。

```java
System.out.println(Stream.of(1,2,3,3).distinct().map(n -> n + "").collect(Collectors.joining(",")));
```

### filter

使用给定的 Predicate 的筛选 Stream 元素，符合条件的留下并组成一个新的 Stream 。

```java
System.out.println(Stream.of(1,2,3).filter(n -> n > 1).map(n -> n + "").collect(Collectors.joining(",")));  
```

### findAny

返回任何一个不确定的元素，通过 Optional 来包装。如果在一个固定不变的组合中，返回第一个元素。

```java
System.out.println(Stream.of(1,2,3).findAny().get());
```

### findFirst

返回第一个元素。

```java
System.out.println(Stream.of(1,2,3).findFirst().get());
```

### flatMap

适用于如果Stream中的元素还是集合，能将集合中的元素组成一个平面的集合。简单来下面的例子，Stream 是二维的，因为 Stream 的元素还是数组，经过flag处理后，变成一维的了，所有元素位于一个Stream 下了。

```java
System.out.println(
		Stream.of(new Integer[]{1,2,3}, new Integer[]{4,5,6}, new Integer[]{7,8,9,0})
		.flatMap(a -> Arrays.stream(a))
		.map(n -> n + "").collect(Collectors.joining(",")));
// 输出：1,2,3,4,5,6,7,8,9,0
```

### forEach

逐个元素执行 Consumer 操作。

```java
Stream.of(1,2,3).forEach(n -> System.out.print(n + ","));
```

### limit 

取出指定个数的元素组成新的 Stream .

```java
System.out.println(Stream.of(1,2,3).limit(2).map(n -> n + "").collect(Collectors.joining(",")));
```

### map 

map 方法的作用是依次对 Stream 中的元素进行指定的函数操作，并将按顺序将函数操作的返回值组合到一个新的 Stream 中。

下面例子将每个元素的值 +1 

```java
System.out.println(Stream.of(1,2,3).map(n -> n + 1).map(String::valueOf).collect(Collectors.joining(",")));
// 输出 2,3,4
```

### max

max 通过给定的比较器，将最大的元素取出来，返回 Optional

```java
System.out.println(Stream.of(1,2,3).max((a, b) -> a - b).get());
```

### min

min 通过给定的比较器，将最小的元素取出来，返回 Optional

```java
System.out.println(Stream.of(1,2,3).min((a, b) -> a - b).get());
```

### noneMatch

noneMatch 于 allMatch, anyMatch 类似，使用给定的 Predicate 检查 Stream 中的所有元素，全部不通过检测则返回 true，否则 false 。

```java
System.out.println(Stream.of(1,2,3).noneMatch(n -> n > 1));
System.out.println(Stream.of(1,2,3).noneMatch(n -> n > 3 || n < 1));
```

### reduce

reduce 的函数操作为二元操作符，一个为前面操作的结果，一个为当前元素，reduce 会逐个对 Stream 中的元素执行指定的操作，并返回最终的结果。

如求和

```java
System.out.println(Stream.of(1,2,3).reduce(0, (a, b) -> a + b));
或者
System.out.println(Stream.of(1,2,3).reduce((a, b) -> a + b).get());
```

### skip

忽略给定个数的元素，返回剩下的元素组成 Stream 。

```java
System.out.println(Stream.of(1,2,3).skip(1).map(n -> n + "").collect(Collectors.joining(",")));
```

### sorted 

通过给定的比较器排序，将排序后的元素的 Stream 返回。

```java
System.out.println(Stream.of(1,2,3).sorted().map(n -> n + "").collect(Collectors.joining(",")));
		System.out.println(Stream.of(1,2,3).sorted((a, b) -> b - a).map(n -> n + "").collect(Collectors.joining(",")));
```


