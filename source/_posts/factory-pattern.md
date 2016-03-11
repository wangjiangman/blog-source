title: "设计模式学习 - 工厂模式"
date: 2013-01-10
tags: 设计模式
categories: 技术
---

在面向对象开发原则中有这么一条：对象要么创建和管理对象，要么使用其他对象，不能兼而有之。这也夫单一职责原则的具体应用。其中创建和管理对象的角色就是工厂，他是工厂模式中的主角，他有如下的职责：

- 创建对象
- 按照约定的规则管理对象

使用工厂能改给我们的好处：

- 将使用者和被使用者的创建过程/创建逻辑解耦（将使用者和被使用者解耦，因为大多数情况下，使用者不知道被使用者的具体类型）。
- 集中创建/管理对象，提高程序的内聚性。

<!--more-->
工厂模式属于创建模式，创建对象者其实就是工厂，与工厂相关的模式有如下几种：

- Simple/Static Factory
- [Factory Method](/factory-method-pattern.html)
- [Abstract Factory](/abstract-factory-pattern.html)
- [Singleton](/singleton-pattern.html)
- Object Pool
- Builder
- Prototype

## Simple/Static Factory

这是最简单的工厂，实际中应用比较常见。就是工厂类提供了一个静态方法，来供使用者调用，从而获取他们需要的对象。

## Factory Method

工厂方法其实是一个特定的模板方法，抽象类中不确定的是创建什么对象，于是将创建对象的逻辑抽象出来，延迟到子类来实现，这遵循了封装变化的原则。类图如下：

![](/images/factory-method.jpg)

在JDK中，List 和 Set 都是继承自 Collection， iterator方法也是Collection中定义的，Collection是一个集合的抽象，根本不知道有哪些实现类，也根本不知道实现类的具体结构，也就根本不知道具体实现类的如何迭代，所以就把生成迭代器的任务交给具体实现类去实现，这样Collection根本不关心具体的Iterator，而和Iterator接口打交道。这就是依赖倒置原则的体现，应该依赖于抽像而不应该依赖于实现。

另外这个模式也是完全符合开闭原则的， 当你有新的集合结构时， 只需要实现Collection， 实现自己的迭代器。根本不需要修改现有代码。

## Abstract Factory

抽象工厂管理的是一组相关的产品。抽象工厂只与产品的抽象关联，而不关心具体的对象，具体的对象有具体的工厂来生成。

考虑下面的场景：在主题管理程序中，目前的主题有windows、mac、linux三种，用户能随时切换自己的系统主题。主题中包括了按钮、选择框、以及标题栏的样式。我们可以利用抽线工厂模式设计如下结构。

![](/images/abstract-factory.jpg)

ThemeFactory 就是抽象工厂，他只是定义了一个生产一系列主题元素的契约，具体的实现有每个具体的主题工厂实现。

源码我已经上传到github，点击这里：[https://github.com/buzheng/buzheng-study-java/tree/master/src/main/java/org/buzheng/study/pattern/abstractfactory](https://github.com/buzheng/buzheng-study-java/tree/master/src/main/java/org/buzheng/study/pattern/abstractfactory)

## Singleton

Singleton模式主要是应用在那些需要在JVM中只保留唯一对象的场景下的。具体的可以参看以前整理的这篇：![设计模式学习 – Singleton Pattern](/singleton-pattern.html)

## Object Pool

Object Pool 也属于创建模式的一种，或者说是也是一种工厂。Object Pool 管理着一组对象，负责对象的创建和销毁，并维护每个对象的状态。使用者从 Object Pool 中获取自己需要的对象，并不负责对象的创建和销毁，仅仅是使用，使用完后再将对象丢给 Object Pool。

## Builder

如果一些对象的构造过程比较复杂，但又可有抽象出公共的步骤，可以考虑使用Builder模式。Builder 模式帮助我们将复杂对象的构建过程和对象的表示分离，是同样的构建过程可以创建不同的对象，所以说Builder模式封装的变化部分是对象的构建过程。看一下结构图：

![](/images/builder.jpg)

由此可以看出Builder模式中的角色：
Director: 负责复杂对象的构建过程
Builder: 负责构建具体的具体步骤
Product：要被构建的对象

在JDK中，StringBuilder 和 StringBuffer 就是一个极其简单的 Builder 模式的实现。他本身充当了 Builder 和 ConcreteBuilder 角色，而 Director 角色则由使用者自己担任， Product角色则是 String 对象。

源码参看这里：[https://github.com/buzheng/buzheng-study-java/tree/master/src/main/java/org/buzheng/study/pattern/builder](https://github.com/buzheng/buzheng-study-java/tree/master/src/main/java/org/buzheng/study/pattern/builder)

## Prototype

这个大概就是通过拷贝对象而生成其他对象的过程。理解不是很透，就不发言了。

总结

工厂模式是我们平时开发中经常用的模式，比如说 IOC 简单来看也是一个工厂；再比如我们在学习策略模式（Strategy）的时候，策略的管理者和生成者很可能就是工厂。