title: "设计模式学习 - Adapter Pattern"
date: 2013-01-18
tags: 设计模式
categories: 技术
---

Adapter Pattern 用以解决当前类和目标接口不匹配的情况，他通过增加一个适配类来是当前类和目标接口达成一致。结构如下：

![](/images/object-adapter.jpg)
<!--more-->

我可以通过这个现实存在的例子来理解 Adapter Pattern：

OrderBiz 是一个已经存在并且运行良好的订单业务类，他可以提供一系列的订单服务，参看如下代码：

``` java
public class OrderBiz { 
	public void addOrder(Order order) {
		System.out.println(&quot;add order: &quot; + order);
	}

	public void modifyOrder(Order order) {
		System.out.println(&quot;modify order: &quot; + order);
	}

	public void deleteOrder(String orderId) {
		System.out.println(&quot;delete order: &quot; + orderId);
	}

	public Order loadOrder(String orderId) {
		System.out.println(&quot;load order: &quot; + orderId);
		return new Order();
	} 
}
```
随着项目的进行，到了后期的维护阶段，随着大家认识的提高和加项目的可维护性，架构师组对订单的操作进行了规划，抽象成了接口 OrderService，要求开发人员进行改进。定义的接口如下：

``` java
public interface OrderService {
	public void add(Order order); 
	public void modify(Order order); 
	public void delete(String orderId); 
	public Order load(String orderId);
}

```
现在问题来了，已经存在类 OrderBiz 和 要求的接口 OrderService 不一致，不匹配。怎么办？ 可能我们很容易想到让 OrderBiz 来实现 OrderService 接口就好了。是啊，很容易，增加几行代码就搞定了。但经验会告诉我们，修改运行良好的类，可能会引入新的问题。哦，这样啊，那还是很容易，增加一个类实现 OrderService 接口，但如何重用以前的逻辑呢？代码重用有两种方式：继承和组合。很好，问题解决。我们就两种方式都来看一下，根据“优先使用组合而非继承”的原则，我们先来个组合的。

``` java
public class OrderServiceAdapter implements OrderService { 
	private OrderBiz orderBiz;

	public OrderServiceAdapter(OrderBiz orderBiz) {
		super();
		this.orderBiz = orderBiz;
	}

	public void add(Order order) {
		this.orderBiz.addOrder(order);
	}

	public void modify(Order order) {
		this.orderBiz.modifyOrder(order);
	}

	public void delete(String orderId) {
		this.orderBiz.deleteOrder(orderId);
	}

	public Order load(String orderId) {
		return this.orderBiz.loadOrder(orderId);
	}
}
```

然后再看个用继承实现的

``` java
public class OrderServiceAdapter extends OrderBiz 
		implements OrderService {

	public void add(Order order) {
		this.addOrder(order);
	}

	public void modify(Order order) {
		this.modifyOrder(order);
	}

	public void delete(String orderId) {
		this.deleteOrder(orderId);
	}

	public Order load(String orderId) {
		return this.loadOrder(orderId);
	}
}
```

其实这两种方式都是 Adapter 的实现，第一种叫做对象适配器（object adapter），第二种叫做类适配器（class adapter）。我们一般会采取对象适配器来完成适配任务，为什么呢？ 优先使用组合而非继承；另外，在java中，只能单继承，继承往往用于上那些语义上确实需要继承的地方。

更多代码请看：https://github.com/buzheng/buzheng-study-java/tree/master/src/main/java/org/buzheng/study/pattern/adapter

在 JDK 中也存在 Adapter Pattern 的应用，比如：

- java.io.InputStreamRead(java.io.InputStream)
- java.io.OutputStreamWriter(java.io.OutputStream)
