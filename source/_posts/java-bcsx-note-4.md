---
title: Java编程思想笔记（四）
date: 2018-08-10 16:52:00
categories: "Java"
tags:
	- Java
	- 书籍笔记
---

## 类型信息

**运行时类型信息使得你可以在程序运行时发现和使用类型信息**

Java是如何让我们在运行时识别对象和类信息。
1. “传统的”RTTI，它假定我们在编译时就已经知道了所有的类型；
2. 另一种是“反射”机制，允许我们在运行时发现和使用类的信息。

> 运行时类型识别(RTTI, Run-Time Type Identification)在Java运行时，RTTI维护类的相关信息，识别一个对象类型。多态(polymorphism)是基于RTTI实现的。RTTI的功能主要是由Class类实现的。
<!-- more -->
### Class对象
> JVM实现的一部分——“类加载器”的子系统去生成类对象。
> 类加载器子系统包含一条类加载器链，但是只有一个原生类加载器。
> 原生类加载器加载的是可信类（包括Java API类），通常是从本地盘加载的。
> 在类加载器链中通常不需要添加额外的类加载器，但是如果有特殊需求（例如在网络中加载类），那么有一种方式可以添加额外的类。
> 所有的类在对其第一次使用时，动态的加载到JVM中。
> Java程序在它开始运行之前并非完全被加，其各个部分是在必需时才加载的。
> 类加载器首先会检查这个类的Class对象是否已经加载，若没有加载，则会去寻找类名.class的文件，其被加载时会被验证，以确保安全和正确。

` Class.forName("ClassName"); `
这个方法是Class类（所有Class对象都属于这个类）的一个static成员。Class对象就和其它对象一样，我们可以获取并操作它的引用（这也就是类加载器的工作）。forName()是取得Class对象的引用的一种方法。它是包含目标类的文本名的的String作输入参数，返回的是一个Class对象引用

#### 类字面常量

Java还提供了类字面常量的方式来生成Class对象的引用。
`ClassName.Class`
这样做不仅更简单而且更安全，因为它在编译时就会受到检查(因此不需要置于try语句块中)。并且它根除了对**forName()**方法的调用，所以也更加高效。
因为**forName()**是运行时才加载，必须置于try语句块中。
另外基本数据类型的包装类有一个`包装类.TYPE`字段对应该`包装类.class`
当使用**.class**去创建对象的引用时，不会自动初始化该类Class对象。
为了使用类做准备的三个步骤
1. **加载**，由类加载器执行，该步骤将查找字节码，并从这些字节码中创建一个Class对象
2. **链接**，在链接阶段将验证类中的字节码，为静态域分配存储空间，并且如果必须的话，将解析这个类创建的对其它类所有引用。
3. **初始化**，如果该类具有超类，则对其初始化，执行静态初始化器和静态初始化块

#### 泛化的Class引用

在JavaSE5中，Class<?>优于Class，即便他们是等价的

```java
Class<? extends Number> c = int.class;
c = double.class;
c = Number.class;
```
这种写法只是为了提供编译器类型检查，直接写Class则会运行时才报错

#### 类转型语法 cast()

```java
public class T1 {
	public static void main(String[] args) {
		A1 a1 = new A2();
		Class<A2> a2Type = A2.class;
		A2 a2 = a2Type.cast(a1);
		a2 = (A2)a1; // or just do this
	}
}

class A1{}
class A2 extends A1{}
```
**cast()**对于无法使用普通转型的情况显得非常有用

**需要记得的是在转型前检查类型是否是我们需要的那种 这里就用到了instanceof  列**
` if(x instanceof Dog) ((Dog)x).wangwang();`
也可使用
`Class.isInstance()`方法提供了一种动态地测试对象的途径。

### 反射：运行时的信息
如果不知道某个对象的确切类型，RTTI可以告诉你。但是有一个限制：这个类型在编译时必须已知，这样才能使用RTTI识别它，并利用这些信息。

如果你需要获取一个指向某个不在你的程序空间中的对象的引用（列如从网络中或者磁盘中获取一串字节，并且被告知这些字节代表一个类）

class类与java.lang.reflect类库一起对反射的概念进行了支持，该类库包含了Field、Method、Constructor类(每个类都实现了Member接口)。这些类型的对象是由JVM在运行是创建的，用以表示未知类里对应的成员。

> 所以RTTI和反射真正的区别只在于，对RTTI来说，编译器在编译时打开和检查.class文件。而反射机制来说，.class文件在编译时是不可获取的，是在运行时打开和检查的.class文件。
```java
//用反射机制遍历类中的方法
try {
	Class<?> c = Class.forName("com.xxx.xxx.ClassName");
		for(Method m : c.getMethods()){
			System.out.println(m.toString());
		}
	} catch (ClassNotFoundException e) {
		e.printStackTrace();
}
```

**反射可以使你访问和修改类的任何私有方法 类 接口，只有final域在遭遇修改时是安全的。运行系统会在不抛出异常的情况下接受任何修改尝试，但是实际上是不会发生任何修改的**

### 动态代理

> 代理是基本的设计模式之一。它是你为了提供额外的或不同的操作，而插入的用来代替“实际”对象的对象。这些操作通常涉及与“实际对象的通信，因此代理通常充当着中间人的角色。

```java
public class T1 {
	public static void consumer(Iface iface){
		iface.doSomething("bobobo");
	}
	
	public static void main(String[] args) {
		RealObject real = new RealObject();
		consumer(real);//未使用代理直接传入真实的对象
		
		ClassLoader loader = Iface.class.getClassLoader();
		Class<?>[] interfaces = new Class[]{Iface.class};
		InvocationHandler h = new DynamicProxyHandler(real);
		
		// Proxy.newProxyInstance 创建动态的代理
		Object obj = Proxy.newProxyInstance(loader,interfaces, h);
		Iface proxy = (Iface)obj;
		consumer(proxy);//使用代理传入处理过后的实际对象
	}
}

class DynamicProxyHandler implements InvocationHandler{
	private Object proxied;
	
	public DynamicProxyHandler(Object proxied) {
		this.proxied = proxied;
	}

	@Override
	public Object invoke(Object proxy, Method method, Object[] args)
	 throws Throwable {
	 //invoke内，对接口的调用将会被重定向为对代理的调用 -- 这句不太理解
		 ((Iface)proxied).doSomething("lalala");
		return method.invoke(proxied, args);
	}
}


interface Iface{
	void doSomething(String arg);
}

class RealObject implements Iface{
	@Override
	public void doSomething(String arg) {
		System.out.println(arg);
	}
	
}
```
动态代理可以将所有调用 重定向 到调用处理器 ，因此通常需要传递一个实际对象，使得调用处理器在执行其中任务时，可以将请求转发。

### 空对象

```java
interface Null{}

class Person{
	Person(String first){}
	
	private static class NullPerson extends Person implements Null{
		private NullPerson() {
			super("None");
		}
		public String toString(){return "NullPerson";}
	}
	public static final Person NULL = new NullPerson();
}
Person p;
if(p == null){
	p = Person.NULL;
}
```

### 总结
1. RTTI 允许通过匿名基类的引用来发现类型信息。但是使用多态会更好。
2. 必须注意的是，不要太早的关注程序的效率问题，这是个诱人的陷阱，最好先让程序运作起来，然后再考虑它的速度，如果要解决效率问题可以用profiler
3. 反射允许更加动态的编程风格