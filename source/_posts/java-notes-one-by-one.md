title: "JAVA 一些基础知识点滴"
date: 2013-01-19
tags: Java
categories: 技术
---

本文主要是记录一些java的基础知识，发现什么记什么，没有什么次序性。<!--more-->

## java 集合类图（粗略）

![](/java-collections.jpg)

## List and Set

List 和 Set 都是 Collection 的子接口，同样是表示2个集合。不同的是，List 允许内部存在重复元素，而 Set 则保证每个元素都是唯一的（Set内不存在满足 e1.equals(e2) == true 这样的2个元素）， Set 只允许一个 null 元素。

## ArrayList

ArrayList的底层实现是数组，所以会有初始化的大小，在添加元素的过程中，如果发现容量不够时，还会对容器扩容。 其初始化大小是 10， 其扩容的算法可以参考方法：ensureCapacity,具体如下：
int newCapacity = (oldCapacity * 3)/2 + 1;

## HashMap

HashMap 采用“链式散列”的结构来存储数据，是基于数组和链表存储的，也就是数组中的元素实际上一个链表结构。有2个参数：初始容量和负载因子。当容量达到初始容量×负载因子的时候，就会扩容，扩容算法就是 当前容量的2倍。

为什么会有负载因子，这是由于HashMap 采用的链式散列这样结构决定的。负载因子越小，冲突的几率越小，查找越快，但是会造成空间的浪费；负载因子越大，空间利用率越高，但是查找的效率越低。

HashMap的初始化容量是16， 负载因数是0.75.

## volatile

一个变量被声明为volatile是告诉JVM这个变量是随时可能变化的，使用的时候必须重新读取变量的值。

## ConcurrentHashMap

Hashtable 和 Collections.sychronizedMap 都是线程安全的，他的每个操作都是线程安全（sychronized）的，但是多个操作组成的操作序列却可能导致数据争用。他们都是在单个锁上进行同步，这意味着每次只有一个线程可以访问集合，该线程访问时，其他线程必须等待。以上问题导致了集合的并发性很差。2个特点：
1， 有条件的线程安全（不支持多个操作序列线程安全）
2， 可伸缩性差（统一时间只能有一个线程进行操作）

java.util.concurrent.ConcurrentHashMap 具有更高的并发性，关键是其使用了锁分离技术，使用多个锁来控制Hash表的不同部分进行的修改。在 ConcurrentHashMap 内部，通过 Segment 将整个 HashMap 划分成多个。