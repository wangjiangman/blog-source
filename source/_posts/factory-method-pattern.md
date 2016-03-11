title: "设计模式学习 - Factory Method Pattern"
date: 2013-05-14
tags: 设计模式
categories: 技术
---

封装变化是我们在设计的时候要遵循的一个基本原则，这也是设计模式的基础。工厂方法这个模式中变化的部分是：创建对象的部分。由于这部分逻辑不确定，抽象类只是定义了一个创建操作接口，具体的实现需要延迟到子类来，由每个具体子类负责创建一个具体的产品，这跟抽象工厂模式是很接近的，可以说工厂方法是<a href="/abstract-factory-pattern.html">抽象工厂</a>的一个特例。下面是一个通用的Factory Method 类图：

![](/images/factory-method.jpg)<!--more-->

举一个JDK中的例子，这是一个典型Factory Method的应用。

我们都知道Collection中定义了一个产生迭代器的方法：iterator，List 和 Set 都是继承自 Collection。很多具体的集合类（比如 ArrayList，LinkedList，HashSet，TreeSet）都实现了 List 和 Set，并且实现了方法iterator方法，返回适合自己的 Iterator。Collection是一个集合的抽象，根本不知道有哪些实现类，也根本不知道实现类的具体结构，也就根本不知道具体实现类的如何迭代，所以就把生成迭代器的任务交给具体实现类去实现，这样Collection根本不关心具体的Iterator，而只是和Iterator接口打交道。这就是依赖倒置原则的体现，应该依赖于抽像而不应该依赖于实现。

另外这个模式也是完全符合开闭原则的， 当你有新的集合结构时， 只需要实现Collection， 实现自己的迭代器，根本不需要修改现有代码。

当然 Iterator 本身也是迭代器模式的实现，点击这里看看我对 [ Iterator Pattern ](/iterator-pattern.html) 的理解。