title: "设计模式学习 - Null Object Pattern"
date: 2013-01-31
tags: 设计模式
categories: 技术
---

Null Object Pattern （空对象模式）是在某些场景下，使用一个 Null Object 来代替返回的 null, 通过这个 NullObject，使用者能够像使用普通对象一样，而不需要 null 判断，让逻辑更顺畅，让代码看起来很干净，很清爽。

那什么是 Null Object？Null Object 就是一个普通对象的特例，他一般继承于普通对象，然后对一些操作进行了重写，让这些操作符合无意义 或 空操作。对这个对象的一切操作，就像没有进行过操作一样。<!--more-->

一般 Null Object Pattern 比较适合于返回空集合的场景，而 JDK 中还对此进行支持。在 Collections 工具类中定义了3个方法，分别返回空的 List，Set，Map：

- <code>java.util.Collections#emptyList()</code>
- <code>java.util.Collections#emptyMap()</code>
- <code>java.util.Collections#emptySet()</code>

需要注意的是，返回的3个对象分别重新实现了List,Map,Set接口， 而且这3个集合/Map都是只读的。

另外一个Null Object 使用的场景就是提供一个默认的操作。这里举例是一个字符处理的例子，要求是这样的：给定一个字符，将字母用()包围，数字用[]包围， 其他字符不处理。且看以下实现。

CharacterHandler 接口定了字符处理操作

``` java
javapublic interface CharacterHandler {
	public String handle(char c);
}
```

DigitHandler 类处理数字字符的算法

``` java
javapublic class DigitHandler implements CharacterHandler {
	@Override
	public String handle(char c) {
		return "[" + c + "]";
	}
}
```

LetterHandler 类处理字母字符的算法

``` java
javapublic class LetterHandler implements CharacterHandler {
	@Override
	public String handle(char c) {
		return "(" + c + ")";
	}
}
```

NullHandler: 定义了默认操作，DefaultHandler可能更适合，但这里主要是为了和 Null Object 模式的名称呼应

``` java
javapublic class NullHandler implements CharacterHandler {
	@Override
	public String handle(char c) {
		return String.valueOf(c);
	}
}
```

HandlerFactory: 算法工厂，没有合适算法时返回null

``` java
javapublic class HandlerFactory { 
	public static CharacterHandler getHandler(char ch) {
		if (Character.isLetter(ch)) {
			return new LetterHandler();
		} else if (Character.isDigit(ch)) {
			return new DigitHandler();
		} 

		return null;
	} 
}
```

HandlerFactoryWithNullHandler: 算法工厂，没有合适算法时返回空对象

``` java
javapublic class HandlerFactoryWithNullHandler { 
	public static CharacterHandler getHandler(char ch) {
		if (Character.isLetter(ch)) {
			return new LetterHandler();
		} else if (Character.isDigit(ch)) {
			return new DigitHandler();
		} 

		return new NullHandler();
	}
}
```

Main: 测试类

``` java
javapublic class Main {

	// 这里采用没有默认操作的算法工厂
	public static String handleCharacter(char ch) {
		CharacterHandler handler = HandlerFactory.getHandler(ch);
		if (handler != null) {
			return handler.handle(ch);
		}

		return String.valueOf(ch);
	}

	// 这里采用有默认操作的算法工厂
	public static String handleCharacter2(char ch) {
		CharacterHandler handler = HandlerFactoryWithNullHandler.getHandler(ch);
		return handler.handle(ch);
	}

	public static void main(String[] args) {
		String str = "1a@b#2c$d";

		for (char ch : str.toCharArray()) {
			System.out.println(handleCharacter(ch));
		}

		for (char ch : str.toCharArray()) {
			System.out.println(handleCharacter2(ch));
		}
	}

}
```

更详细代码请单击这里：https://github.com/buzheng/buzheng-study-java/tree/master/src/main/java/org/buzheng/study/pattern/nullobject