---
title: 备忘录模式(Memento)
date: 2018-10-26
categories: 设计模式
tags: 学习笔记
description: 
---
#### 一、什么是备忘录模式
##### 1. 定义
在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样以后可将该对象恢复到原先保存的状态。

##### 2. 应用备忘录模式解决问题的思路
备忘录模式引入一个存储状态的备忘录对象，为了让外部无法访问这个对象的值，一般把这个对象实现为需要保存数据的对象的私有内部类。这样除了这个需要保存数据的对象，外部无法访问到这个备忘录对象的数据，保证了对象的封装性不被破坏。

这个备忘录对象需要存储在外部。为了避免让外部访问到这个对象内部数据。备忘录模式引入一个备忘录对象的窄接口，这个接口一般是空的，这样外部存储的地方，只是知道存储了一些备忘录接口的对象，但接口是空的，它们无法通过接口访问备忘录对象的内部数据。

##### 3. 结构说明
[结构示意图]

- Memento：备忘录对象。主要用来记录原发器对象需要保存的状态。与普通的封装数据的对象不同是，备忘录对象一般只让原发器对象来操作；为了保证这点，会把备忘录对象作为原发器对象的内部类来实现，而且实现成私有的，这就断绝了外部来访问这个备忘录对象的途径。为了与外部交互，通常备忘录对象会实现一个窄接口，来标识对象的类型。
- Originator：原发器。使用备忘录来保存某个时刻原发器自身的状态，也可以使用备忘录来恢复内部状态。
- Caretaker：备忘录管理者。主要负责保存备忘录对象。
	- 广义地说，调用原发器获得备忘录对象后，备忘录对象放在哪里，哪个对象就可以是管理者对象。
	- 管理者对象并不是只能管理一个备忘录对象，一个管理者对象可以管理很多的备忘录对象。
	- 狭义的管理者对象只管理同一个类的备忘录对象；广义的管理者对象是可以管理不同类型的备忘录对象。
	- 管理者对象需要实现的基本功能主要是：存入备忘录对象，保存备忘录对象和获取备忘录对象。
	- 管理者可以存取备忘录对象，但不能访问备忘录对象的内部数据。

##### 4. 示例代码
```java
/**
 * 备忘录的窄接口，没有任何方法定义
 */
public interface Memento {
	//
}

/**
 * 原发器对象
 */
public class Originator {
	private String state = "";
	/**
	 * 创建保存原发器对象的状态的备忘录对象
     */
	public Memento createMemento() {
		return new MementoImpl(state);
	}

	public void setMemento(Memento memento) {
		MementoImpl impl = (MementoImpl)memento;
		this.state = mementoImpl.getState();
	}

	private static class MementoImpl implements Memento {
		private String state = "";
		public MementoImpl(String state) {
			this.state = state;
		}

		public String getState() {
			return state;
		}
	}
}

public class Caretaker {
	private Memento memento = null;
	
	public void saveMemento(Memento memento) {
		this.memento = memento;
	}

	public Memento retriveMemento() {
		return this.memento;
	}
}
```

备忘录模式的功能，首先是在不破坏封装性的前提下，捕获一个对象的内部状态。

###### 为什么要捕获这个对象的内部状态呢？捕获这个内部状态有什么用？
是为了在后续的某个时候，将该对象的状态恢复到备忘录所保存的状态，这才是备忘录真正的目的。

###### 捕获的状态存放在哪里？
备忘录模式中，捕获的内部状态存储在备忘录对象中；而备忘录对象通常会被存储在原发器对象之外，也就是被保存状态的对象的外部，通常是存放在管理者对象那里。

###### 窄接口和宽接口

##### 5. 使用备忘录的潜在代价
标准的备忘录模式的实现机制是依靠缓存来实现的，因此，当需要备忘的数据量较大时，或者是存储的备忘录对象数据量不大但是数量很多的时候，或者是用户很频繁地创建备忘录对象的时候，这些都会导致非常大的开销。

##### 6. 结合原型模式
如果原发器对象中全部或大部分的状态都需要保存，一个简洁的方式就是直接克隆一个原发器对象。

##### 7. 离线存储
离线存储的实现也很简单，主要修改管理者对象，把它保存备忘录对象的方法实现成保存到文件中，而恢复备忘录对象实现成读取文件就可以了。

#### 二、备忘录模式的优缺点
##### 优点
- 更好的封装性：通过使用备忘录对象，来封装原发器对象的内部状态，有效地保证了对原发器对象内部状态的封装，不把原发器对象的内部实现细节暴露给外部。
- 简化了原发器：备忘录对象被保存到原发器对象之外，让客户来管理他们请求的状态，从而让原发器对象得到简化。


##### 缺点
- 可能会导致高开销：对备忘录对象的存储和恢复，它的基本实现方式就是缓存备忘录对象。如果需要缓存的数据量很大，或特别频繁地创建备忘录对象，开销很大。

#### 三、何时选用备忘录模式
- 必须保存一个对象在某个时刻的全部或部分状态，方便在以后需要时可以恢复到先前的状态，可以使用备忘录模式。
- 需要保存一个对象的内部状态，且不想暴露对象的实现细节，以及不想破坏该对象的封装性，就可以使用备忘录模式。


