---
title: Java编程思想笔记（二）
date: 2018-07-27 16:52:00
categories: "Java"
tags:
	- Java
	- 书籍笔记
---

## 访问权限控制

package语句，必须是文件中除注释以外的第一句程序代码。
**需注意的是，Java包的命名规则全部使用小写字母，包括中间的字也是如此。**

` import static com.PackName.xxClass.*` 语句可以让你在代码中直接使用静态的导入方法

内部类可以用任何一种访问修饰符修饰

控制对成员的访问权限有两个原因。
	1. 是为了使用户不接触它们不该接触的部分，同使也简化客户端开发对于类的理解。
	2. 确保不会有任何客户端开发依赖于某个类的底层实现的任何部分
<!--more-->

## 复用类

### 继承与组合
为了继承，一般规则是将所有的数据成员都指定为private，将所有的方法指定为public。

若是父类有带参构造器需要用` supen(args) `显示调用

组合技术通常用于想在新类中使用现有类的功能而非接口 （ has - a 有一个）
继承则是使用某个现有类，并开发一个它的特殊版本。通常，这意味着你在使用一个通用类，并为了某种特殊需要而将其特殊化。（ is - a 是一个）
是否使用继承考虑是否需要从新类向基类**向上转型**

### 总结

> 开始一个设计时，一般应优先选择使用组合（或者可能是代理），只有确实必要时才用继承。因为组合更加灵活。此外，通过对成员类型使用继承技术的添加技巧，可以在运行时改变那些成员对象的类型和行为。当你开始设计一个系统时，应该认识到程序开发是一种增量过程，犹如人类的学习一样，这一点很重要。程序依赖于实验，你可以尽可能去分析，但当你开始执行一个项目时，你任然无法知道所有的答案。如果将项目视作是一种有机的、进化着的生命体而去培养，而不是打算盖摩天大楼一样快速见效，就会获得更多和更迅速的回馈。继承和组合正是在面向对象程序设计中使你可以执行这种实验的最基本的两个工具。

### final 关键字

通常它指“这是无法改变的”，可能出于这两种理由：设计或者效率。
一般可能使用到**final**的三种情况：数据、方法、类。

#### final 数据

有时数据的恒定不变是很有用的，比如：
1. 一个永不改变的编译时的常量。
2. 一个在运行时被初始化的值，而你不希望它被改变。
编译器可以将常量代入任何可能用到它的计算式中，也就是说，可以在编译时执行计算式。这减轻了一些运行时的负担。在Java中这类常量必须是基本数据类型，并且以关键字**final**表示。在对常量进行定义的时候，必须对其进行赋值。
一个既是**static**又是**final**的域只占据一段不能改变的存储空间。
当对象引用不是基本数据类型使用**final**时，对于对象引用，使其引用恒定不变。一旦引用被初始化指向一个对象，就无法再把它改为另一个对象。然而，对象其自身却是可以被修改的，Java并没有提供使任何对象恒定不变的途径（但可以自己编写类是取得恒定不变的效果）。数组也一样，它也是对象。

> 根据惯例，**static 、final** 同时修饰的域（编译期常量）将用大写表示。字间用下划线隔开。

Java允许生成“空白final”，但编译器需确保空白final在使用前必须被初始化。**必须在域的定义处或者每个构造器中用表达式对final进行赋值**,final 也可用在参数中，这一特性主要用于匿名内部类传递数据。
```java
public class t1 {
	private final int j = 2;
	private final int i;
	t1(final int a){
		i = a;
	}
}
```

----------

#### final 方法

使用final方法的原因有两个
1. 把方法锁定，防止继承类修改它的含义。这是出于设计的考虑，确保在继承中使方法行为保持不变，并且不会被覆盖。
2. 效率。<Java SE5/6 上的版本中已经不再需要了>

类中所有的private方法都隐式的指定为是final的。由于无法取用private方法，所以也就无法覆盖它。可以给private方法添加final修饰，但这并没有添加任何额外的意义。


----------

#### final 类

当将某个类的整体定义为final时，就表明了你不打算继承该类，而且也不允许别人这么做。换句话说，出于某种考虑，你对该类的设计永不需要做任何改动，或者出于安全考虑，你不希望它有子类。** 由于final类是禁止继承的，所以final类中的所有方法都隐式的指定为是final的，因为无法覆盖他们 **

> 有关final的忠告
> 在设计类时，将方法指明是final的，应该说是明智的。

## 多态

> 多态的作用则是消除类型之间的耦合关系
> 通过动态绑定实现多态
> 所做的代码修改，不会对程序中其它不应受影响的部分产生破坏
> 只有非private方法才可以覆盖

构造器内部的多态行为
```java
class Person1{
	void eat(){System.out.println("eat.eat.eat..");}
	Person1(){
		eat();
	}
}

class Student1 extends Person1{
	private int eatNum = 4;
	Student1(int eatNum){
		this.eatNum = eatNum;
		System.out.println("Student1.Student1.eatNum="+eatNum);
	}
	@Override
	void eat() {
		System.out.println("Student1.eat.eatNum="+eatNum);
	}
}

public class t1 {
	public static void main(String[] args) {
		new Student1(3);
	}
}
/*Output:
* Student1.eat.eatNum=0
* Student1.Student1.eatNum=3
*/
```
1. 为啥第一个等于0？
	初始化的实际过程是：
	1. 在其它任何事物发生之前，将分配给对象的存储空间初始化成二进制的零。
	2. 基类构造器调用的是子类覆盖的 eat() 方法，由于1的原因 `eatNum = 0`
	3. 按照声明的顺序调用成员的初始化方法
	4. 调用子类的构造器主体
2. 编写构造器的准则
	**用尽可能简单的方法使对象进入正常状态，如果可以的话，避免调用其它方法**

## 接口

使用接口的核心原因：
1. 为了向上转型为多个基类型（以及由此带来的灵活性）。
2. 与使用抽象基类相同，防止客户端创建该类的对象
3. 如果某事物应该成为一个基类，那么第一选择应该是使它成为一个接口。

因为重载方法仅通过返回值是分不开的，所以若是在覆盖实现接口方法中方法仅返回值不同则会发生混乱，编译器会报错。

在Java SE5 之前通常使用接口来定义常量，因为你放入接口中的任何域都自动隐式是**static**和**final**的。之后使用更为强大的**enum**。

### 嵌套接口

接口方法可以嵌套在类或者其它接口中
```java
class Obj implements A.B{
	private interface C{}
	class InObj implements C{}
}
interface A{
	interface B{}
}
```

### 接口与工厂

接口是实现多重继承的途径，而生成遵循某个接口的对象典型方式就是工厂方法设计模式。这与直接调用构造器不同，我们在工厂对象上调用的是创建方法，而该工厂对象将生成接口的某个实现的对象。

```java
public class SimpleFactory {
	public Product createProduct(int type){
		switch (type) {
		case 1:
			return new ConcreteProduct1();
		case 2:
			return new ConcreteProduct2();
		default:
			return new ConcreteProduct3();
		}
	}
}

class Client{
	public static void main(String[] args) {
		SimpleFactory sf = new SimpleFactory();
		Product p = sf.createProduct(1);
	}
}

interface Product{}

class ConcreteProduct1 implements Product{}

class ConcreteProduct2 implements Product{}

class ConcreteProduct3 implements Product{}
```

## 内部类

> 可以将一个类定义放在另一个类的定义内部，这就是内部类。
> 不止是可以名字隐藏和组织代码模式，当生成一个内部类的对象时，此对象与制造它的**外围对象**之间就有了一种联系，所以它能访问其外围对象的所有成员，而**不需要**任何特殊条件。

```java
public class t1 {
	void f(){}
	Inner inner(){
		return new Inner();
	}
	
	class Inner{
		public t1 outer(){
			return t1.this; //生成对外部类对象的引用
		}
	}
	
	public static void main(String[] args) {
		t1 t = new t1();
		
		Inner in = t.inner();
		in.outer().f();
//		|| 		
//		Inner in = t.new Inner();
//		in.outer().f();
	}
}
```

> 在拥有外部类的对象之前是不可能创建内部类对象的，因为内部类对象会暗暗地链接到创建它的外部类对象上。**静态内部类不需要**。所以普通的内部类不能有static数据和static字段和static类。


### 内部类与向上转型

> 当将内部类向上转型为其基类，尤其是转型为一个接口时，内部类就有了用武之地。内部类对某个接口的实现可以完全不可见，并不可用。所得到的只是指向基类或接口的引用，所以能够很方便的隐藏实现细节。

```java
public class t1 {
	class SmallStudent implements Student{
		private int age;
		SmallStudent(int age) {
			this.age = age;
		}
	}
	
	public Student student(int age){
		return new SmallStudent(age);
	}
	
	public static void main(String[] args) {
		t1 t = new t1();
		Student s = t.student(2);
	}
}

interface Student{}
```

### 在方法和作用域内的内部类

```java
//在方法作用域的类-局部内部类
public class t1 {
	Student student(int age){
		class SmallStudent implements Student{
			private int age;
			public SmallStudent(int age) {
				this.age = age;
			}
		}
		return new SmallStudent(age);
	}
}

interface Student{}
```
也可以嵌入if等语句的作用域类

### 匿名内部类

```java
public class t1 {
	Student student(int age){	
		return new Student() { //相当于 StudentSub implements Student{}
			@Override
			public int getAge() {
				return 0;
			}
			public int value(){
				return 1;
			}	
		};
			
	}
}

interface Student{
	int getAge();
}

```
在这个匿名内部类中使用了默认的构造器来生成Student。如果需要参数，那么你需要创建一个带参构造器的**类**。`new XXClass(x,y){};`

> jdk7 之前若是你的匿名内部类希望在类中使用其外部定义的对象，参数引用需为final为了解决生命周期不同的问题，匿名内部类备份了变量，为了解决备份变量引出的问题，外部变量要被定义成final我们匿名内部类使用final不是怕修改，是怕不能同步修改。
> 值得注意的是，从JDK1.8开始，编译器不要求自由变量一定要声明为final，如果这个变量在后面的使用中**没有发生变化**，就可以通过编译，Java称这种情况为“effectively final”。 若是**发生变化**则无法通过编译。

```java
int answer = 42; // jdk8 上 不用强制使用final
Thread t = new Thread(new Runnable() {
    public void run() {
        System.out.println("The answer is: " + answer);
   }
});

int answer = 42;
Thread t = new Thread(
    () -> System.out.println("The answer is: " + answer)
);

int answer = 42;
answer ++; // don't do this !编译报错
Thread t = new Thread(
   () -> System.out.println("The answer is: " + answer)
 );
```

#### 结合工厂设计模式
```java
public class SimpleFactory {
	public Product createProduct(int type){
		switch (type) {
		case 1:
			return new Product(){};
		case 2:
			return new Product(){};
		default:
			return new Product(){};
		}
	}
}

class Client{
	public static void main(String[] args) {
		SimpleFactory sf = new SimpleFactory();
		Product p = sf.createProduct(1);
	}
}

interface Product{}
```

### 嵌套类

> 如果不需要内部类对象与其外围类对象之前有联系，那么可以将内部类声明为static。这通常称为**嵌套类**，
> 普通的内部类不能有static数据和static字段和static类，嵌套类则可以。
> 嵌套类不需要外围类对象，不能访问非静态的外围对象。
> 嵌套类可以作为接口的一部分，因为放到接口中的任何类都是public 和 static 的。

### 为什么需要内部类？

> 一般来说，内部类继承自某个类或实现某个接口，内部类的代码操作创建它的外围类的对象。所以可以认为内部类提供了某种进入其外围类的窗口。
> **每个内部类都能独立地继承自一个（接口的）实现，所以无论外围类是否已经继承了某个（接口的）实现，对于内部类都没有影响**
> 主要解决**’多重继承‘**问题
```java
class A{}
class B{}
class C{
	A funA(){
		return new A(){};
	}
	B funB(){
		return new B(){};
	}
}
```

### 闭包与回调

> 闭包是一个可调用的对象，它记录了一些信息，这些信息来自于创建它的作用域。
> 通过回调，对象能够携带一些信息，这些信息允许它在稍后某个时刻调用初始的对象。回调的价值在于它的灵活性，可以在运行时动态的决定调用什么方法
> 通过内部类提供闭包的功能是优良的解决方案。
```java
class MyIncrement{
	void increment(){System.out.println("other operation");} 
	static void f(MyIncrement mi){mi.increment();}
}

class Caller{
	private Incrementable CallbackRef;
	public Caller(Incrementable CallbackRef) {
		this.CallbackRef = CallbackRef;
	}
	void go(){CallbackRef.increment(1);}
}

interface Incrementable{
	void increment(int i);
}

class Callee1 implements Incrementable{
	private int i = 0;
	@Override
	public void increment(int j) {
		System.out.println(++i);
	}
}

class Callee2 extends MyIncrement{ //MyIncrement 已经有了一个increment方法，与
	private int i =0;
	@Override
	void increment() {//复写MyIncrement的increment方法
		super.increment();
		System.out.println(++i);
	}
	
	private class Closure implements Incrementable{ //以提供返回Callee2的“钩子（hook）”
		@Override
		public void increment(int j) { //复写Incrementable的increment方法
			System.out.println("Another operation"); 
			Callee2.this.increment(); //Closure使用此引用回调Callee2类
		}
	}
	
	Incrementable getCallbackRef(){
		return new Closure();
	}
}

public class t1 {
	public static void main(String[] args) {
		Callee1 c1 = new Callee1();
		Callee2 c2 = new Callee2();
		MyIncrement.f(c2);
		Caller cr1 = new Caller(c1);
		Caller cr2 = new Caller(c2.getCallbackRef());
		cr1.go();
		cr1.go();
		cr2.go();
		cr2.go();

	}
}
```

### 内部类与控制框架

> 控制框架是一类特殊的应用程序框架，它用来解决响应事件的需求。主要用来响应**事件驱动系统**。应用程序设计中常见的问题之一是图形用户接口（GUI），它几乎完全是事件驱动系统。它的工作就是在事件“就绪”的时候执行事件。

> 内部类允许
> 1. 控制框架的完整实现是由单个的类创建的，从而使得实现的细节被封装起来。内部类用来表示解决问题所必需的各种不同的action()。
> 2. 内部类能够很容易地访问外围类的任意成员，所以可以避免这种实现变得笨拙。

### 内部类的继承

> 因为内部类的构造器必须连接到指向其外部类对象的引用，所以在继承内部类时必须显示的用语法来明确他们之间的关系
> 内部类不会因外部类被继承而被覆盖

```java
public class T1 {
	class T2{}
}

class T3 extends T1.T2{
	T3(T1 t1) {
		t1.super();
	}
}

```

----------

> 局部内部类和匿名内部类具有相同的行为和能力，且局部内部类的名字在方法外是不可见的。使用区别则是：我们需要一个已命名的构造器，或这需要重载构造器，而匿名内部类只能用于实例化。另一个原因是，需要不止一个该内部类的对象。
> **内部类标识符** ：外围类名+$+内部类名+.class
> 若是匿名内部类则会把内部类名变成一个简单的数字