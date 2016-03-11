title: "软件架构模式的种类"
date: 2014-02-15
tags: 
categories: 技术
---

在做软件架构设计时,根据不同的抽象层次可分为三种不同层次的模式：架构模式(Architectural Pattern)、设计模式(Design Pattern)、代码模式(Coding Pattern)。

架构模式是一个系统的高层次策略，涉及到大尺度的组件以及整体性质和力学。架构模式的好坏可以影响到总体布局和框架性结构。

设计模式是中等尺度的结构策略。这些中等尺度的结构实现了一些大尺度组件的行为和它们之间的关系。模式的好坏不会影响到系统的总体布局和总体框架。设计模式定义出子系统或组件的微观结构。

代码模式（或成例）是特定的范例和与特定语言有关的编程技巧。代码模式的好坏会影响到一个中等尺度组件的内部、外部的结构或行为的底层细节，但不会影响到一个部件或子系统的中等尺度的结构，更不会影响到系统的总体布局和大尺度框架。<!--more-->

## 架构模式(Architectural Pattern)

一个架构模式描述软件系统里的基本的结构组织或纲要。架构模式提供一些事先定义好的子系统，指定它们的责任，并给出把它们组织在一起的法则和指南。称之为系统模式。

- MVC模式,一个架构模式常常可以分解成很多个设计模式的联合使用。MVC模式常常包括调停者（Mediator）模式、策略（Strategy）模式、合成（Composite）模式、观察者（Observer）模式等。
- Layers（分层）模式，有时也称Tiers模式
- Blackboard（黑板）模式
- Broker（中介）模式
- Distributed Process（分散过程）模式
- Microkernel（微核）模式

架构模式常常划分成如下的几种：

一、 模块结构(From Mud to Structure)型。帮助架构师将系统合理划分，避免形成一个对象的海洋。包括Layers（分层）模式、Blackboard（黑板）模式、Pipes/Filters（管道/过滤器）模式等。

二、分散系统（Distributed Systems）型。为分散式系统提供完整的架构设计，包括像Broker（中介）模式等。

三、人机互动（Interactive Systems）型，支持包含有人机互动介面的系统的架构设计，例子包括MVC（Model-View-Controller）模式、PAC（Presentation-Abstraction-Control）模式等。

四、Adaptable Systems型，支持应用系统适应技术的变化、软件功能需求的变化。如Reflection（反射）模式、Microkernel（微核）模式等。

## 设计模式(Design Pattern)

一个设计模式提供一种提炼子系统或软件系统中的组件的，或者它们之间的关系的纲要设计。设计模式描述普遍存在的在相互通讯的组件中重复出现的结构，这种结构解决在一定的背景中的具有一般性的设计问题。

设计模式常常划分成不同的种类，常见的种类有：

创建型设计模式，如工厂方法（Factory Method）模式、抽象工厂（Abstract Factory）模式、原型（Prototype）模式、单例（Singleton）模式，建造（Builder）模式等

结构型设计模式，如合成（Composite）模式、装饰（Decorator）模式、代理（Proxy）模式、享元（Flyweight）模式、门面（Facade）模式、桥梁（Bridge）模式等

行为型模式，如模版方法（Template Method）模式、观察者（Observer）模式、迭代子（Iterator）模式、责任链（Chain of Responsibility）模式、备忘录（Memento）模式、命令（Command）模式、状态（State）模式、访问者（Visitor）模式等等。

以上是三种经典类型，实际上还有很多其他的类型，比如Fundamental型、Partition型，Relation型等等。设计模式在特定的编程语言中实现的时候，常常会用到代码模式。比如单例（Singleton）模式的实现常常涉及到双检锁（Double-Check Locking）模式等。

## 代码模式(Coding Pattern)

代码模式（或成例）是较低层次的模式，并与编程语言密切相关。代码模式描述怎样利用一个特定的编程语言的特点来实现一个组件的某些特定的方面或关系。

较为著名的代码模式的例子包括双检锁（Double-Check Locking）模式等。
<blockquote>备注：以前收藏的一篇文章，忘记出处了。</blockquote>