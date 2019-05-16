---
title: 桥接模式(Bridge)
date: 2018-10-26
categories: 设计模式
tags: 学习笔记
description: 
---
#### 一、什么是桥接模式
##### 1. 定义
将抽象部分与它的实现部分分离，使它们都可以独立地变化。

##### 2. 应用桥接模式来解决问题的思路
当抽象和实现混杂在一起时，会导致一个纬度的变化引起另一个纬度进行相应的变化，从而使得程序扩展起来非常困难。

要想解决这个问题，就必需把这个两个纬度分开，也就是将抽象部分和实现部分分开，让它们相互独立，可以独立的变化，使扩展变得容易。

桥接模式通过引入实现的接口，把实现部分从系统中分离出去。为了让抽象部分能方便地与实现结合起来，把顶层的抽象接口改成抽象类，在其中持有一个具体的实现部分的实例。

##### 3. 结构和说明
[结构示意图]

- Abstraction：抽象部分的接口。通常在这个对象中，要为一个实现部分的对象引用，抽象对象里面的方法，需要调用实现部分的对象来完成。这个对象中的方法，通常都是和具体业务相关的方法。
- RefinedAbstraction：扩展抽象部分的接口。通常在这些对象中，定义跟实际业务相关的方法，这些方法的实现通常会使用Abstraction中定义的方法，也可能调用实现部分的对象来完成。
- Implementor：定义实现部分的接口。这个接口不用和Abstraction中的方法一致，通常由Implementor接口提供基本的操作。而Abstraction中定义的是基于这些基本操作的业务方法，也就是说Abstraction定义了基于这些基本操作的较高层次的操作。
- ConcreteImplementor：真正实现Implementor接口的对象。

##### 4. 示例代码
```java
public abstract class Abstraction {
    /**
     * 持有一个实现部分的对象
     */
    protected Implementor impl;

    public Abstraction(Implementor impl) {
        this.impl = impl;
    }

    /**
     * 示例操作，实现一定的功能，可能需要转调实现部分的具体实现方法
     */
    public void operation() {
        impl.operationImpl();
    }


}

public class RefineAbstraction extends Abstraction{
    public RefineAbstraction(Implementor impl) {
        super(impl);
    }
}

public interface Implementor {
    void operationImpl();
}


public class ConcreteImplementor implements Implementor {
    @Override
    public void operationImpl() {

    }
}

public class ConcreteImplementorB implements Implementor {
    @Override
    public void operationImpl() {

    }
}
```

##### 5. 什么是桥接
桥接，通俗点说就是在不同的东西之间搭个桥，让它们能够连接起来，可以相互通讯和使用。在桥接模式中是为被分离了的抽象部分和实现部分进行搭桥。

##### 6. 如何桥接
只要让抽象部分拥有实现部分的接口对象，就桥接上了，在抽象部分通过这个接口来调用具体实现部分的功能。

##### 7. 为什么需要桥接
为了达到让抽象部分和实现部分都可以独立变化的目的，在桥接模式中，是把抽象部分和实现部分分离开来的。因此需要桥接让抽象部分可以调用到实现部分的功能。

##### 8. 谁来桥接
所谓谁来桥接，就是谁来负责创建抽象部分和实现部分的关系，也就是谁来负责创建Implementor对象，并把它设置到抽象部分的对象中去。大致有如下集中方式：

- 由客户端负责创建Implementor对象，并在创建抽象部分对象的时候，把它设置到抽象部分的对象中去。
- 可以在抽象部分对象构建时，由抽象部分的对象自己来创建相应的Implementor对象，当然可以给它传递一些参数，它可以根据参数来选择并创建具体的Implementor对象。
- 使用抽象工厂或简单工厂来选择并创建具体的implementor对象，抽象部分的来可以通过工厂的方法来获取Implementor对象。
- 如果使用Ioc/DI容器的话，可以通过IoC容器来创建具体的Implementor对象，并注入到Abstraction中。


##### 9. 对设计原则的体现

- 很好实现了开闭原则：通常应用桥接模式的地方，抽象部分和实现部分都是可变化的，也就是应用会有两个变化的纬度，桥接模式就是找到这两个变化，并分别封装起来，从而合理地实现OCP。
- 很好体现了多用对象组合，少用对象继承：


#### 典型例子——JDBC
通常我们写的应用程序，是面向JDBC的API开发，这些接口相当于桥接模式中抽象部分的接口。

JDBC的驱动程序实现了JDBC的API，驱动程序就相当于桥接模式中的具体实现部分。而不同的数据库会有不同的驱动实现。

桥接的方式，不再是让抽象部分持有实现部分，而是采用类似工厂的做法，通过DriverManager来把抽象部分和实现部分对接起来，从而实现抽象部分和实现部分解耦。


#### 二、桥接模式的优缺点
##### 优点

- 分离抽象和实现部分：桥接模式分离了抽象部分和实现部分，从而提高了系统的灵活性。让抽象部分和实现部分独立开来，分别定义接口，这有助于对系统进行分层，从而产生更好的结构化的系统。
- 更好的扩展性：由于把抽象部分和实现部分分离开了，而且分别定义接口，这就使得抽象部分和实现部分可以分别独立地扩展，而不互相影响，从而提高了系统的可扩展性。
- 可动态地切换实现
- 减少子类的数量

#### 三、何时选用桥接模式

- 如果不希望在抽象部分和实现部分采用固定的绑定关系，可以采用桥接模式，来把抽象部分和实现部分分离开，然后在程序运行期间来动态设置抽象部分需要用到的具体的实现。
- 如果出现抽象部分和实现部分都能扩展的情况，可采用桥接模式，让抽象部分和实现部分独立地变化。
- 如果采用继承的实现方案，会导致产生很多子类，可以考虑使用桥接模式，分析功能变化的原因，看看是否能分离成不同的纬度，然后通过桥接模式来分离他们，从而减少子类的数目。