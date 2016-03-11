title: "设计模式学习 - Decorator Pattern"
date: 2013-02-03
tags: 设计模式
categories: 技术
---

Decorator Pattern （装饰者模式）提供了动态增加对象行为的方式，他在原有对象的基础上动态的增加额外的职责。基本结构如下：

![](/images/decorator.jpg)
<!--more-->

Decorator 模式的重点在于动态增加新的职责，一般体现为新增一个操作（方法）。这与代理模式是有明显区别的，代理是在保持原有接口的前提下，在原有行为的前后加入其他操作。

如果我们想增加新的职责，只需一个新的实现类就好了，采用“优先使用聚合” 的原则，并不会影响到现有的类，也符合“开闭原则”。缺点就是如果需要增加的职责很多，就要增加很多的新类，容易造成类爆炸。

java.io包中大量采用了decorator模式，比如 BufferedInputReader(InputReader) ， BufferedInputReader就在InutReader的定义的操作的基础上又增加了readLine操作，更加方便对字符的处理。以下是 java.io  的大致类图。

![](/images/io-inputstream.jpg)

![](/images/io-outputstream.jpg)

![](/images/io-reader.jpg)

![](/images/io-writer.jpg)