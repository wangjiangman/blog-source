title: "设计模式学习 – State Pattern"
date: 2013-05-17
tags: 设计模式
categories: 技术
---
模式解决问题的思路都是相同的，找到变化的部分封装起来，用来解决某一类特定的问题。状态模式同样是用来出来多路分支的情况，不过在岔口要根据对象的状态来进行选择，对象的不同状态决定了选择不同的分支。既然状态是变化的，我们就考虑将状态封装，由上下文保存当前的状态，由状态对象决定下一步的操作。<!--more-->

![](http://upload.wikimedia.org/wikipedia/commons/thumb/e/e8/State_Design_Pattern_UML_Class_Diagram.svg/400px-State_Design_Pattern_UML_Class_Diagram.svg.png)

我们考虑现实中的一个列子，就是那个煤气灶上的开关，开关控制着开和关以及火力的大小。FireSwitch就是这个开关，他保存着当前的状态，相当于状态的上下文。State 则抽象了不同的状态，具体的状态则有关闭（OffState），小火（SmallState），中火（MediumState），大火（LargeState）。

这是一个类图：

![](/images/state.jpg)

贴一下具体的代码：

``` java
package org.buzheng.study.pattern.state;

public class FireSwitch {
	private State state;

	public FireSwitch() {
		state = new OffState();
	}

	public void setState(State state) {
		this.state = state;
	}

	public void switchClockwise() {
		this.state.switchClockwise(this);
	}

	public void switchCounterClockwise() {
		this.state.switchCounterClockwise(this);
	}
}
```

``` java
package org.buzheng.study.pattern.state;

public interface State {

	public void switchClockwise(FireSwitch fs);

	public void switchCounterClockwise(FireSwitch fs);
}
```


``` java
package org.buzheng.study.pattern.state;

public class OffState implements State {

	public void switchClockwise(FireSwitch fs) {
		fs.setState(new SmallState());
		System.out.println("off -> small");
	}

	public void switchCounterClockwise(FireSwitch fs) {
		fs.setState(new LargeState());
		System.out.println("off -> large");
	}

}
```


``` java
package org.buzheng.study.pattern.state;

public class SmallState implements State {

	public void switchClockwise(FireSwitch fs) {
		fs.setState(new MediumState());
		System.out.println("small -> medium");
	}

	public void switchCounterClockwise(FireSwitch fs) {
		fs.setState(new OffState());
		System.out.println("small -> off");
	}

}
```


``` java
package org.buzheng.study.pattern.state;

public class MediumState implements State {

	public void switchClockwise(FireSwitch fs) {
		fs.setState(new LargeState());
		System.out.println("medium -> large");
	}

	public void switchCounterClockwise(FireSwitch fs) {
		fs.setState(new SmallState());
		System.out.println("medium -> small");
	}

}
```


``` java
package org.buzheng.study.pattern.state;

public class LargeState implements State {

	public void switchClockwise(FireSwitch fs) {
		fs.setState(new OffState());
		System.out.println("large -> off");
	}

	public void switchCounterClockwise(FireSwitch fs) {
		fs.setState(new MediumState());
		System.out.println("large -> medium");
	}

}
```


``` java
package org.buzheng.study.pattern.state;

/**
 * 
 *
 */
public class Main {
	public static void main(String[] args) {
		FireSwitch fs = new FireSwitch();

		fs.switchClockwise();
		fs.switchClockwise();
		fs.switchClockwise();
		fs.switchClockwise();

		System.out.println("-----------------------");

		fs.switchCounterClockwise();
		fs.switchCounterClockwise();
		fs.switchCounterClockwise();
		fs.switchCounterClockwise();
		fs.switchCounterClockwise();
		fs.switchCounterClockwise();
		fs.switchCounterClockwise();
		fs.switchCounterClockwise();
	}
}
```

&nbsp;