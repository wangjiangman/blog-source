title: "设计模式学习 - Template Method Pattern"
date: 2013-03-13
tags: 设计模式
categories: 技术
---

Template Method （模板方法），从他的名字我们就可以看得出，这个模式抽象为一个模板。模板方法为我们解决某个算法提供了一个抽象的实现骨架，确定的步骤可以直接实现，不确定（有变化）的部分，则推迟到具体的子类来实现。算法的使用者只需依赖于模板，具体使用哪个模板，则由上下文指定。<!--more-->

比如在一个电子商务系统中，订单查询是很重要一个功能。需求是这样的，给定一个订单的ID，能按照不同的需求返回不同的格式，比如html格式的，json格式的等。我们拆解需求，发现这个实现主要分为2个步骤：

- 根据订单号查询出订单的信息
- 将订单的信息按照需要的格式输出

第一步没什么可说的，可以从数据库/缓存中，只要把订单信息查询出来皆可以了。第二步则是变化的部分，要支持html，json。难道我要在代码中写 if else 或者 switch case 么？万一以后出个pdf 的需求怎么办？显然if else /switch case 违背了开闭原则，那么如何封装变化呢？ 模板方法会帮助我们的，我们只要把第二部抽象出来，剩下的不同实现让子类搞定就是了。看一下具体的代码实现吧。

QueryOrder 类就是定义的模板了：

``` java 
package org.buzheng.study.pattern.templatemethod;

public abstract class QueryOrder {

	public String queryOrder(String orderId) {
		Order order = this.getOrderInfo(orderId);
		return this.formatOrderInfo(order);
	}

	private Order getOrderInfo(String orderId) {

		Order order = new Order();
		order.setOrderId("123456");
		order.setAmount(1000.0);

		return order;
	}

	// 只留给子类来实现
	protected abstract String formatOrderInfo(Order order);

}
```

QueryOrderHtmlImpl 类 就是 HTML 输出的实现了。

``` java 
package org.buzheng.study.pattern.templatemethod;

public class QueryOrderHtmlImpl extends QueryOrder {

	@Override
	protected String formatOrderInfo(Order order) {
		return String.format("<table><tr><td>OrderId</td><td>%s</td></tr><tr><td>Amount</td><td>%s</td></tr></table>", 
				order.getOrderId(), String.valueOf(order.getAmount()));
	}

}
```

QueryOrderJsonImpl 类就是 json 输出的实现了。

``` java 
package org.buzheng.study.pattern.templatemethod;

public class QueryOrderJsonImpl extends QueryOrder {

	@Override
	protected String formatOrderInfo(Order order) {
		return String.format("{userId:%s, amount:%s}", order.getOrderId(), String.valueOf(order.getAmount()));
	}

}
```

Order 类 就是 订单信息类。

``` java 
package org.buzheng.study.pattern.templatemethod;

public class Order {
	private String orderId;
	private Double amount;

	public String getOrderId() {
		return orderId;
	}
	public void setOrderId(String orderId) {
		this.orderId = orderId;
	}
	public Double getAmount() {
		return amount;
	}
	public void setAmount(Double amount) {
		this.amount = amount;
	}	
}
```

这个就是测试类：

``` java 
package org.buzheng.study.pattern.templatemethod;

public class Main {
	public static void main(String[] args) {
		// 默认格式输出
		QueryOrder qo = new QueryOrderImpl();
		System.out.println(qo.queryOrder("123456"));

		// json格式输出
		qo = new QueryOrderJsonImpl();
		System.out.println(qo.queryOrder("123456"));

		// html格式输出
		qo = new QueryOrderHtmlImpl();
		System.out.println(qo.queryOrder("123456"));

	}
}
```

上面这个测试类可能过于简单，如何根据要求选取具体的模板是一个复杂的算法，很有可能需要到工厂。

模板方法是一个是有多个实现步骤的算法的抽象，为算法的整体抽象出一个骨架，而不确定或者易变化的步骤推迟到子类来分别实现，完全遵守了开闭原则。

扩展一下，<a title="设计模式学习 – Factory Method Pattern" href="/factory-method-pattern.html">工厂方法（factory method) 模式</a>也是模板方法的一个特例，只不过工厂方法中不确定的部分是创建对象而已。