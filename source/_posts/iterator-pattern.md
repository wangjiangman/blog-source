title: "设计模式学习 - Iterator Pattern"
date: 2013-01-17
tags: 设计模式
---

Iterator Pattern 属于行为模式，她为如何访问集合的内部定义了统一的访问方式。有了这个统一的访问方式，那些集合类的使用者对所有的集合都可以使用统一的方式来访问，而不关心是什么集合。下面是一个通用的结构图

![](/images/iterator.jpg)
<!--more-->

JDK中的接口 Iterator 就是一个很好的实现。首先，她是一个接口，抽象了一系列访问集合内部元素的操作。参考 java.util.Iterator, 我们可以看到她定义了3个操作：

- boolean hasNext() // 检查是否还有更多元素
- E next() // 获取下一个元素
- void remove() // 删除当前元素

Collection中定义了iterator方法，由其实现类实现自己的迭代器，因为只有实现类才知道自己的内部结构。这样保证了所有Collection 的子类有一致的访问方式。

注意，一般JDK中的Iterator实现都是fail-fast机制的，也就是如果在遍历的过程中，有其他的 进程/线程 删除/修改集合，则会抛出异常 java.util.ConcurrentModificationException