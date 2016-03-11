title: "设计模式学习 - Proxy Pattern"
date: 2013-05-03
tags: 设计模式
categories: 技术
---

Proxy Pattern，代理模式。代理是我们经常用到的一个概念，比如我们常用的代理服务器，还有一些公司业务的代理人等，都是代理。我们平时只与代理打交道，把我们的要求告诉代理，然后又代理帮助我们去处理，处理完成后，再将结果反馈给我们。

我们先看一下 Proxy Pattern 的类图。

![](/images/proxy.jpg)

proxy pattern class diagram

代理服务器：是用来代理上网的，我们设置好代理服务器之后，所有的请求都发往代理服务器，代理服务器会帮助我们完成下载网页，传输数据等操作。同时代理服务器也可以统计流量，过滤一些内容等。

从代理服务器我们可以看出，代理不但能帮助我们完成功能，还可以在流程中添加一些控制（统计流量、过滤内容），这对我们很有启发。比如在我们平时的业务处理层中，某些业务处理类专职于处理某中业务逻辑，而此时如果我们需要添加一些权限控制，安全性控制，或者日志服务等，如果直接修改业务类原有代码，肯定不是最佳的实践。不但违反了开闭原则，也违反了类的单一职责原则，导致过多的职责都添加到业务类中，使业务类除了业务逻辑外还负责了许多本不该他负责的职责。 下面的就是一个简单的日志代理的例子。

IHello接口定义个了一个操作sayHello。

``` java
package org.buzheng.study.pattern.proxy;

public interface IHello {

	public void sayHello(String name);

}
HelloSpeaker是IHello的一个具体实现。

``` java
package org.buzheng.study.pattern.proxy;

public class HelloSpeaker implements IHello {

	public void sayHello(String name) {
		System.out.println("hello " + name);
	}

}
```

HelloStaticProxy是一个代理类，负责添加日志的功能，在操作的前后都记录日志。

``` java
package org.buzheng.study.pattern.proxy;

public class HelloLoggingProxy implements IHello {

	private IHello hello;

	public HelloLoggingProxy(IHello hello) {
		super();
		this.hello = hello;
	}

	public void sayHello(String name) {
		System.out.println("start hello.....");
		hello.sayHello(name);
		System.out.println("end hello.....");
	}

	public static void main(String[] as) {
		IHello helloProxy = new HelloLoggingProxy(new HelloSpeaker());
		helloProxy.sayHello("buzheng");
	}
}
```

由此我们可以得出，代理是能够让被代理类能够更专注于自己的职责，其他的控制有代理类来完成；代理模式不会改变对外公布的接口，也不会改变原有类的逻辑，而是在流程的前后添加一些控制；代理类只能代理一种类型（接口）的类，如果需要代理的类的类型（接口）太多，则会引入太多的代理类，很可能带来类爆炸，增加维护的难度，这种情况，我们可以使用动态代理来解决。

假如我们还有如下类需要代理，看代码：

``` java
package org.buzheng.study.pattern.proxy;

public interface IBye {
	public void sayBye(String name);
}
package org.buzheng.study.pattern.proxy;

public class ByeSpeaker implements IBye {

	public void sayBye(String name) {
		System.out.println(name + " bye bye.");
	}

}
```

这个时候以前的类HelloLoggingProxy就不再适用了。按照以前的方法，如果想要代理IBye，则需要重新创建新类ByeLoggingProxy，这给我们带来了维护的难度。Java 的动态代理是个不错的选择，有了它，我们就不用手动创建代理类，通过Java 的反射及时，他会自动的创建代理类，并且不局限单个接口，可以支持任意接口，就像下面的例子一样：

``` java
package org.buzheng.study.pattern.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Arrays;

public class LoggingDynamicProxy implements InvocationHandler {

	private Object delegate;

	public Object bind(Object delegate) {
		this.delegate = delegate;
		Class clazz = delegate.getClass();

		return Proxy.newProxyInstance(clazz.getClassLoader(), clazz.getInterfaces(), this);
	}

	public Object invoke(Object proxy, Method method, Object[] args)
			throws Throwable {		
		Object result = null;

		System.out.println("method start.......");
		System.out.println(this.delegate.getClass());
		System.out.println(proxy.getClass());
		System.out.println("method name: " + method.getName());
		System.out.println("method args: " + Arrays.toString(args));
		result = method.invoke(delegate, args);
		System.out.println("end start.......");

		return result;
	}

	public static void main(String[] args) {
		IHello helloProxy = (IHello) new LoggingDynamicProxy().bind(new HelloSpeaker());
		helloProxy.sayHello("buzheng");

		IBye byeProxy = (IBye) new LoggingDynamicProxy().bind(new ByeSpeaker());
		byeProxy.sayBye("buzheng");
	}
}
```

可以看出这个类LoggingDynamicProxy接可以代理IHello，也可以代理IBye，当然还可以代理你想到的任意接口，只要实现了接口的类都可以。当然被代理类必须实现接口也是动态代理的局限。了解了动态代理之后，是不是觉得有点AOP的意思呢？ ^_^