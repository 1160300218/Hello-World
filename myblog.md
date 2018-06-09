# 为了复用的几种模式-Design Patterns for Reuse

## 一. 前言
2018年春季学期，新开的**`《软件构造》`**是一门细节颇多，极具实用价值的课程。想要融会贯通想来实践理论缺一不可。为了整理上课的学习内容并加深自己的理解，我尝试整理知识点写成博客。希望能为今后的复习提供方便。

## 二. 复用性
### *复用性的优点
在我看来，复用性在设计大型项目时极具优势。复用性好的程序意味着能够为更多有关联性的的开发提供便利，节省大量时间，精力和资金。类，算法，数据结构都可以得到复用。在程序量越来越大的今天，开发好的程序离不开群体的智慧。利用别人开发的库和框架，改写自己原来的代码等等，都是对代码的复用。实际上，我们在编写java和c++等程序时，经常使用栈，队列等数据结构，就是在对别人封装好的函数库进行使用。想想看，正是因为有了这么庞大的API，这些程序语言使用起来何其便捷？


## 三. 为了复用的几种模式

这些为了复用的模式，总体上分为两大类六小类。

  + 结构模式 **Structural patterns**
    * 适配器模式 **Adaptor**
    * 装饰模式 **Decorator**
    * 外观模式 **Facade**   
  + 行为模式 **Behavioral patterns**
    + 策略模式 **Strategy**
    + 模版模式 **Template method**
    + 迭代器模式 **Iterator**  
 
下面我们就来一一介绍这六个模式。  

---
### 1. 结构模式 Structural patterns
#### 1.1 适配器模式 Adaptor
适配器模式在实现时可以分为三个应用场景。即*类适配器*，*对象适配器*，*接口适配器*。  

##### 1.1.1 类适配器
我们可以想象这样一个场景，我们的程序需要调用类A中的方法，但是A中的方法并不是我们想要的；我们想要的是类B中实现的方法，但是B中的该方法并不符合我们想要的接口规范。这时候，我们就可以设计一个Adaptor类，在其中起到类似转换器的作用。 

	public class A {  \\类A
		void A() {
		//A 所能实现的代码
		}  
	}  	
	public interface B {  \\接口B
		void B();
	}  
	public class Adaptor implements B extends A{ \\适配器类，我们的程序可以直接调用。
		@Override
		void B() {
			super.A();
		}
	}  

由上述代码能够清楚地看到Adaptor类是如何实现作用的。其实，这种方法可能很多人都无意识地用到过吧，只是没有把它往理论上靠。  

##### 1.1.2 对象适配器	
对象适配器和类适配器的使用场景是极为相似的，甚至它们的作用也是几乎相同的。只是类适配器是基于继承**subtyping**的方法适配，而对象适配器是根据组合**composition**的原理实现适配。

	public class A {  \\类A
		void A() {
		//A 所能实现的代码
		}  
	}  	
	public interface B {  \\接口B
		void B();
	}  
	public class Adaptor implements B { \\适配器类，我们的程序可以直接调用。
		private A a;
		//constructor
		public Adaptor(A a) {
			this.a = a;
		}
		
		@Override
		void B() {
			this.a.A();
		}
	}  
	
显然，对象适配器主要是在适配器类中加入了一个私有的类A的实例，再在接口符合规范的实现重写的类B中使用该实例的方法。我们的程序调用并构建该适配器类时需要传入一个实例。  

##### 1.1.3 接口适配器	
接口适配器模式于上述两种适配器有着较为显著的不同。当存在一个这样的接口，我们想要使用它的一大堆方法中的其中几个，如果我们直接实现该接口，许多不需要重写的方法也必须要实现。这样一来，整个类就变的极为臃肿。这时候，我们可以使用一个抽象类来做适配器，再继承该抽象类，使方法的实现变得更简洁可用。具体实例如下所示：

	public interface A { \\一个定义了极多方法的接口
		void A1();
		void A2();
		void A3();
		void A4();
		void A5();
		void A6();
	}
	public abstract class Adaptor implements A() { \\适配器
		void A1() {}
		void A2() {}
		void A3() {}
		void A4() {}
		void A5() {}
		void A6() {}
		
	}
	public class B extends Adaptor() {
		@Override void A1() { //想要实现的方法
		//代码
		}
	}
	
这样一看，是不是类B就非常地简洁明了呢？  

---  
上面所讲的三种适配器模式的应用场景也可以实现对偶，即这是我们想要复用别人的代码，如果我们要让自己的代码更易于复用呢？不也可以使用适配器模式提供各式各样的接口给用户吗？  

#### 1.2 装饰模式 Decorator 
装饰模式是一个非常好用的模式。当我们拥有好几个特殊的栈，比如可以撤销操作的`undostack`,比如需要输入密码才能使用的`securestack`,比如一个同步栈`synchronizedstack`，只允许一个线程读取。而此时我们想要的是同时可以撤销操作和输入密码的`undosecurestack`,允许撤销的同步栈`undosynchronizedstack`,需要输入密码的同步栈等等等等，难道这时候我们要一个一个地将这些栈都实现吗？这显然也太麻烦了。这时候我们就可以用到`Decorator`装饰器。  
装饰器利用了继承**subtyping**和委托**delegation**的原理。结构图如下所示：  
  
![装饰模式的结构示意图](http://chuantu.biz/t6/327/1528534821x-1566661145.png)  
 
* `Component`接口定义了装饰器`Decorator`所能完成的操作。  
* 我们可以通过定义`ConcreteComponent`中的操作实现对各种各样的具体对象的产生。  
* `Decorator`是一个抽象类，并且是几种不同“装饰”`ConcreteDecorator`的父类。装饰器其中的抽象方法交由其子类更具体的装饰器实现。  
  
---  

老师在上课时为我们讲述的冰淇淋的例子，更易于理解。有个光秃秃的冰淇淋`plainicecream`,我们可以在顶部为它加上各种各种的装饰`topping`。比如`candytopping`,`nutstopping`,`peanuttopping`等等。顾客总是要求多多，因此我们要能生产出各种各样不同种类的冰淇淋。代码如下：  

	public interface IceCream { //顶层接口 
		void AddTopping();
	}
	public class PlainIceCream implements IceCream{ //基础实现，无添加冰激凌 
		@Override
    	public void AddTopping() {
    		System.out.println("Plain IceCream ready for some toppings!");
    	}
	}
	/*装饰器基类*/
	public abstract class ToppingDecorator implements IceCream{ 
		protected IceCream input;  
    	public ToppingDecorator(IceCream i){
			input = i; 
		}
		public abstract void AddTopping(); //留给具体装饰器实现  
	}    	public class CandyTopping extends ToppingDecorator{ 
		public CandyTopping(IceCream i) {
			super(i); 
		}
		public void AddTopping() {
			input.AddTopping(); //decorate others first   
			System.out.println("Candy Topping added!");
		} 
	}
	public class NutsTopping extends ToppingDecorator{   
		//similar to CandyTopping
	}
	public class PeanutTopping extends ToppingDecorator{ 
		//similar to CandyTopping
	}

则在我们调用时，采用**递归**的调用方法，一层一层地构建我们想要的冰淇淋。  

	public class client {
		public static void main(String[] args) {
			IceCream a = new PlainIceCream(); 
			IceCream b = new CandyTopping(a); 
			IceCream c = new PeanutTopping(b); 
			IceCream d = new NutsTopping(c); d.AddTopping();
			//or
			IceCream toppingIceCream =
				new NutsTopping(
					new PeanutTopping(
            			new CandyTopping(
                 		new PlainIceCream()
						) 
					)
    			);
			toppingIceCream.AddTopping();
		}
	}
	
根据上述的代码进行调用，可以得到输出结果：  

	Plain IceCream ready for some toppings! 
	Candy Topping added!
	Peanut Topping added!
	Nuts Topping added!

就这样，`Decorator`模式是我们不需要单独写出每个具体类的代码，而是充分利用了代码的可复用性，大大减少了工作量，还增强了使用的灵活性。

#### 1.3 外观模式 Facade	
一言以蔽之，外观模式实际上是创建了高层次的接口使得底层的系统更便于用户使用。外观模式可以隐藏哪些复杂的结构，只提供给外部一个简单的接口。这样的模式是符合编程理念中封装和安全性考虑的。  
在外观模式中，可以分为三个主要的部分： 
  
* `具体实现功能的子系统模块`：这部分的功能和结构都较为复杂。  
* `外观模块`：提供了较为简单的接口，用户可以直接调用的部分。通过这个模块，用户间接操控具体实现功能的子系统模块。
* `客户模块`：该模块无法直接使用具体实现功能的子系统模块，只能通过外观模块来完成自己想要的功能。  

我认为，在许多场合中，外观模式都被广泛应用。比如计算机想要开关机是需要处理多个部件的，但是这些都交给了中层的外观模块去解决，用户只需要给出开关机的命令即可。可以得见，外观模式是十分用户友好的。同样，该模式也有利于我们编写复杂的程序，将底层和较高层分开。  

---  

### 2. 行为模式 Behavioral patterns 
#### 2.1 策略模式 Strategy
策略模式在编写程序时应用极广。比如当我们在对某些输入数据进行排序时，我们常常会用到多种排序方式，因为不同的排序方法适用于不同的环境。这时候，我们可以为实现同一种功能的不同算法创建一个接口，再根据不同的算法实现该接口。是不是很简单？策略模式利用了多态的特性，难怪被称作`“算法的多态”`。策略模式的结构如下：  
![策略模式的结构图](http://chuantu.biz/t6/327/1528545492x-1566660619.png)  
该模式非常简单，就不详细说了。  

#### 2.2 模版模式 Template method  
模版模式的定义是：在一个操作中给出算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以不改变算法的结构即可重定义该算法的某些特定步骤。通俗点的理解就是 ：完成一件事情，有固定的个数步骤，步骤实现的顺序也相同，但是每个步骤根据对象的不同，而实现细节不同。这时就可以在父类中定义一个完成该事情的总方法，按照完成事件需要的步骤去调用其每个步骤的实现方法。每个步骤的具体实现，由子类完成。  

#### 2.3 迭代器模式 Iterator 
迭代器模式提供一种方法顺序访问一个聚合对象中各个元素, 而又无须暴露该对象的内部表示。在遍历一个聚合对象时，把在元素之间游走的责任交给迭代器，而不是聚合对象。该模式的优点如下：
    
* 它支持以不同的方式遍历一个聚合对象，并简化了聚合类。  
* 在同一个聚合上可以有多个遍历。
* 在迭代器模式中，增加新的聚合类和迭代器类都很方便，无须修改原有代码。  

但同时，迭代器模式也可能会造成一些问题：由于迭代器模式将存储数据和遍历数据的职责分离，增加新的聚合类需要对应增加新的迭代器类，类的个数成对增加，这在一定程度上增加了系统的复杂性。  

---

## 四.总结 
总体来说，这几种模式所蕴含的思想还是非常贴近现实的。其中很多理念可以在许多方面寻找到。当然，只是写下理论并不代表全部掌握，实践也是非常重要的。多敲代码多思考，一点点进步！























