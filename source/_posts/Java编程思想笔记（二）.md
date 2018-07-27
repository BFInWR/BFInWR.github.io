---
title: Java编程思想笔记（二）
date: 2018-07-27 16:52:00
categories: "Java"
tags:
	- Java
	- 书籍笔记
description: "记录《Thinking in Java》一书的笔记"
---
# Java编程思想笔记（二）

## 访问权限控制

package语句，必须是文件中除注释以外的第一句程序代码。
**需注意的是，Java包的命名规则全部使用小写字母，包括中间的字也是如此。**

` import static com.PackName.xxClass.*` 语句可以让你在代码中直接使用静态的导入方法

内部类可以用任何一种访问修饰符修饰

控制对成员的访问权限有两个原因。
	1. 是为了使用户不接触它们不该接触的部分，同使也简化客户端开发对于类的理解。
	2. 确保不会有任何客户端开发依赖于某个类的底层实现的任何部分


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

## 接口与工厂

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

