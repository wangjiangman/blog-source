title: "instanceof, isInstance, isAssignableFroms"
date: 2014-04-14
tags: Java
categories: 技术
---

在一些工具代码或者通用操作逻辑中，在运行时了解一个对象的实际类型有时候是很有必要的。对于如何检查当前对象的类型，或者与父类型、其他类型进行比较，Java为我们提供了如下途径：

- 操作符 instanceof
- Class.isInstance
- Class.isAssignableFrom
<!--more-->

## instanceof

instanceof 是一个操作符，用来检查一个对象是否某个类型，其用法参考如下代码：

``` java
new HashMap() instanceof Map<
```

用法：X instanceof Y。X 为需要检查的具体对象，Y是要判断的类型。上面那句的意思就是比较 HashMap对象是不是一个 Map 类型。

## Class.isInstance()

isInstance是Class对象的一个方法，其作用和 instanceof 相同，不过这个主角成了类对象，被比较的对象作为参数传入而已。其用法参考如下代码：

``` java
Map.class.isInstance(new HashMap())
```

用法：X.isInstance(Y) 。X 是类型对应的类对象，Y 需要检查的具体的对象。

不过从方法的语义顺序上来看，逆向理解好一些。

## Class.isAssignableFrom()

isAssignableFrom 用来检验本类型是否和 参数的类型、参数类型的父类型、参数类型的父接口类型一样。其用法如下代码所示：

``` java
HashMap.class.isAssignableFrom(Map.class)
```

这个方法对用来判断是否可以安全类型转换很有帮助，正如我们理解的那样，子类能安全的转为父类，而父类强制转为子类则有可能带来数据的丢失或隐藏。

以下是我学习时的一个demo，从结果中可见一斑。

``` java
package org.buzheng.study.clazz;

import java.util.HashMap;
import java.util.LinkedHashMap;
import java.util.Map;

public class TypeCheck {

	@SuppressWarnings("rawtypes")
	public static void main(String[] args) {

		/*
		 * instanceof 比较一个对象是否某个类型（包括父类、接口）
		 * 用法：X instanceof Y (X 需要检查的具体的对象，Y 是类型的名字）
		 */
		System.out.println(new HashMap() instanceof Map);
		System.out.println(new HashMap() instanceof HashMap);
		System.out.println(new HashMap() instanceof LinkedHashMap);
		System.out.println(new LinkedHashMap() instanceof HashMap);
		System.out.println(null instanceof Map);
		System.out.println((HashMap) null instanceof Map);

		System.out.println("------------------------");

		/*
		 *  isInstance 和 instanceof 一样，只是主角变成了 类
		 *  用法：X.isInstance(Y) (X 是类型对应的类对象，Y 需要检查的具体的对象)
		 *  用来判断参数对象是否当前类型的一个实例
		 */
		System.out.println(Map.class.isInstance(new HashMap()));
		System.out.println(HashMap.class.isInstance(new HashMap()));
		System.out.println(LinkedHashMap.class.isInstance(new HashMap()));
		System.out.println(HashMap.class.isInstance(new LinkedHashMap()));
		System.out.println(Map.class.isInstance(null));
		System.out.println(Map.class.isInstance((HashMap)null));

		System.out.println("------------------------");

		/*
		 * isAssignableFrom 用来检验本类型是否和 参数的类型、参数类型的父类型、参数类型的父接口类型一样。
		 * 用法：X.isAssignableFrom(Y)  (X 类型对象，Y要检验的类型对象)
		 */
		System.out.println(HashMap.class.isAssignableFrom(Map.class));
		System.out.println(Map.class.isAssignableFrom(HashMap.class));
		System.out.println(Map.class.isAssignableFrom(LinkedHashMap.class));

	}

}
```
