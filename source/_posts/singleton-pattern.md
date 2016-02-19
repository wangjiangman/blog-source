title: "设计模式学习 - Singleton Pattern"
date: 2013-01-05
tags: [设计模式,单例模式]
---
Singleton Pattern 是一种创建模式，根据译者的不同，可能会译作：单态模式、单例模式、单件模式等。Singleton Pattern的目的是保证某个对象在运行环境中有且只有一个实例。主要的实现思路就是将构造方法私有化（防止由使用者构建对象），然后由对象本身来确保这唯一的实例，并提供一个公共操作来供使用者来获取这唯一的实例。<!--more-->

![单例模式](/images/singleton.jpg) 

这里列举几种实现方式：

## 直接初始化实现

我们很容易根据模式的目标设计出实现，请看如下代码：

``` java
public class Singleton {

	private Singleton() {
		System.out.println("init Singleton");
	}

	private static Singleton instance = new Singleton();

	public static Singleton getInstance() {
		return instance;
	}
}
```


我们先看一下实现方法： 

1、将构造器私有化，即：private Singleton; 
2、提供一个公共操作供使用获取实例，即：public static Singleton getInstance()。 
3、内部有个静态变量来维护这个实例：private static Singleton instance = new Singleton(); 各种Singleton模式的实现区别主要在于instance初始化时机和初始化策略上。直接初始化实现的好处就是简单，适用各种场景。这种方式在类加载时会创建对象，本例中是由getInstance()调用创建对象。引起类加载的方式很多，很有可能在不需要创建的时候创建了对象，若对象很小则没有问题，若对象很大则比如会造成内存的浪费。于是我们想到可以使用懒加载的方式来实现，如下：

## 懒惰初始化实现（Lazy Init）

Lazy Init的意思就是只有到了需要的时候才生成，如果已经生成了，就不在生成，直接返回已有对象，看下面的代码便一目了然。


``` java
public class LazySingleton {
	private LazySingleton() {}

	private static LazySingleton instance = null;

	public static LazySingleton getInstance() {
		if (instance == null) {
			instance = new LazySingleton();
		}

		return instance;
	}
}
```

Lazy init 的方式是在使用者使用的时候才初始化对象，避免了在使用之前生成对象占用空间，造成内存的浪费。显然这中实现方式中由于存在竞争条件，则不适用于并发的场景下，于是有了“双重检查锁实现（double check locking）”。

## 双重检查锁实现（Double Check Locking）

Double check locking 意图是对懒加载的改进，已避免懒加载方式在多线程下的问题，就如下面的代码所示：

``` java
public class DoubleCheckSingleton {
	private DoubleCheckSingleton() {} 
	private static DoubleCheckSingleton instance;

	public DoubleCheckSingleton getInstance() {  
		if (instance == null) {
			synchronized (this) {
			if (instance == null) {
				instance = new DoubleCheckSingleton();
			}
			}
		}  
		return instance; 
	}
}
```

这个实现在C/C++下完全没有问题，在Java环境下，这种方式是不被推荐的，甚至是反对的使用的。由于java内存模型的问题（无序写入），会在使用过程中引入错误：JVM在启动对象初始化操作后，就返回了，加入此时有其他线程来请求，instance已经不为null，而初始化还未完成，如果使用，会带来一些错误。

## 内部静态类

使用内部静态类来维护唯一的对象实例，不但可以实现懒加载，也不会Double check locking中的问题。如下所示：

``` java
public class InnerStaticClassSingleton {

	private InnerStaticClassSingleton() {}

	static class SingletonHolder {
		private static InnerStaticClassSingleton instance = new InnerStaticClassSingleton();
	}

	public static InnerStaticClassSingleton getInstance() {
		return SingletonHolder.instance;
	}
}
```

JVM 的内部机制会保证在并发环境下也只会创建唯一的实例。

## 枚举（Enum）

使用枚举类型的特性来实现单例也是一个不错的选择。不但能够避免多线程的同步问题，而且还能防止反序列化重新创建新的对象。唯一不足的是这种方式看起来有些古怪，虽然达到了目标，确并不符合Singleton的结构。

``` java
public enum EnumSingleton {
	INSTANCE {
		public void method() {
			System.out.println("instance......");
		}
	};

	public abstract void method();
}
```

Singleton提供了一种维护一个单一对象实例的解决方案，这种方案在肯定不能适合所有的环境，比如分布式环境等。 

随着IOC的流行，越来越多的人提出抛弃Singleton（Singleton是邪恶的），而让容器来保证实例的个数。其实我想这并不是绝对的，IOC容器只能保证在这个容器中的实例个数，然而在一个JVM环境中，可能只有一个容器。模式不是公式，不同的环境可能需要不同的解决方案。 

