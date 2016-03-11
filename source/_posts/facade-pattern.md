title: "设计模式学习 - Facade Pattern"
date: 2013-01-08
tags: 设计模式
categories: 技术
---

Facade Pattern 被译成门面模式。他的目的就是想对外部用户隐藏系统/模块/类的复杂性，提供给用户一个简单易用的使用接口。<!--more-->

这是一个实际存在的场景：
OrderService 封装了操作Order的相关逻辑：增删改查；在目前的系统中运行良好。

现在有一个新的需求，外部其他模块（或其他系统）需要查询订单的信息，如果将 OrderService 全部暴露出去，会带来风险，比如其他系统可能会将订单删除。此时可以使用门面模式，只提供客户需要的操作。

这是本例中的URL类图：

![](/images/class-facade.jpg)

系统中的已有逻辑类：OrderService.java

``` java
public class OrderService {
    public void add(Order order) {
        System.out.println("add order: " + order);
    }
 
    public void modify(Order order) {
        System.out.println("modify order: " + order);
    }
 
    public void delete(String orderId) {
        System.out.println("delete order: " + orderId);
    }
 
    public Order load(String orderId) {
        System.out.println("load order: " + orderId);
        return new Order();
    }
}
```

提供给外部客户使用的查询类：OrderQueryServiceFacade

``` java
public class OrderQueryServiceFacade {
    private OrderService orderService;
 
    public OrderQueryServiceFacade(OrderService orderService) {
        super();
        this.orderService = orderService;
    }
 
    /*
    * 这里是需要暴露的操作，不一定与原接口相同。
    * 比如下面的 queryById
    */
    public Order load(String orderId) {
        return this.orderService.load(orderId);
    }
 
    public Order queryById(String orderId) {
        return this.load(orderId);
    }
}
```

这是一个简单的例子，简单到只是隐藏了类的一些操作。更多Facade应用场景是隐藏整个系统（模块）的复杂性，对外部提供一个简单的操作界面，就如下图展示的一样。

![](/images/system-facade.jpg)

比如，电子商务系统中，商品管理系统（负责所有商品的管理）一个复杂的系统，作为下游其他系统（比如说移动应用）可能需要关心所有的信息，他们可能只需要查询某些商品的信息，比如（库存量、描述信息等），如果直接和系统内部交互，复杂不说，也是不安全的，这时候可以单独提供一个商品中心的门面出来（具体的实现可以采用：RMI，Hessian，或者Web Service）。下游系统只需要可这个门面进行交互，而不关心商品管理系统内部的复杂度。
