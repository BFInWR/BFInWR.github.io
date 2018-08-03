---
title: Java编程思想笔记（一）
date: 2018-07-25 16:52:00
categories: "Java"
tags:
	- Java
	- 书籍笔记

---
> 记录《Thinking in Java》一书的笔记

## 初始化与清理

### this

当发送消息给对象时，编译器做了一些幕后工作。它暗自把“所操作对象的引用”作为第一个参数传递给该对象调用的方法。
```java
    FuncClass.method(funcObj1,args1);
    FuncClass.method(funcObj2,args2);
```
- 由于这个引用是由编译器“偷偷”传入的，所以没有标识符可用。但是，为此有个专门的关键字：**this**。
- **this**关键字只能在方法内部使用，表示对“调用方法的那个对象”的引用。用法和对象的引用一样。但要注意如果在方法的内部调用同类的另一个方法，就不必使用this，直接调用即可，编译器会自动帮你添加。

>  **只有当需要明确指出对当前对象的引用时，才需要使用this关键字**
<!--more-->
例如，当需要返回当前对象的引用时，就常常在return语句中这样写 
```java
    return this;
```
this关键字对于将当前对象传递给其他方法也很有用
```java
    class Person{
	    public void eat(Apple apple){
		    Apple peeled = apple.getPeeled();
	    }
    }
    class Peeler{
	    static Apple peel(Apple apple){
		    return apple;
	    }
    }
    class Apple{
	    Apple getPeeled(){
		    return Peeler.peel(this);  
	    }
    }
```
Apple需要调用Peeler.peel()方法，它是一个外部的工具方法，为了将自身传递给外部方法，Apple必须使用this关键字。
- this 还可用于在构造器中调用构造器，但只能调用一个且必须将其调用置于最起始处，否则编译器报错。
- static方法没有this关键字。

### 清理

1.对象可能不被垃圾回收。
2.垃圾回收并不等于“析构”。

**finalize()**的用途何在？
一旦GC准备好释放对象占用的内存，将首先调用finalize()方法，并且下次GC动作发生时，才会真正回收对象占用内存，**3.垃圾回收只与内存有关**。之所以要有finalize()，是由于在分配内存时可能采用类C语言中的做法，而非Java中的通常做法。这种情况主要发生在使用**“本地方法”**的情况下，本地方法是一种在Java中调用非Java代码的方式。本地方法目前只支持C和C++，但它们可以调用其它语言写的代码，所以实际上可以调用任何代码。在非Java代码中，也许会调用C的**malloc()**函数系列来分配存储空间，而且除非调用**free()**函数，否则存储空间将得不到释放，从而造成内存泄漏。当然free()是C和C++中的函数，所以需在finalize()中用本地方法调用它。
**所以它确实不是进行普通清理工作使用的。而且绝对不能直接调用finalize()，但可以用于验证*终结条件*以发现程序缺陷 **

----------

**垃圾回收（GC）器**如何工作？
存储空间的释放会影响存储空间的分配。
堆的实现像是一个传送带，没分配一个新对象，它就往前移动一格。
当创建足够多的对象后，内存资源将耗尽，GC介入,它一边回收空间，一边使堆中的对象紧凑排列，这样“堆指针”就可以很容易移动到更靠近传送带的开始处，也避免了页面错误（频繁的内存页面调度）。

**GC机制**
- *引用记数*常用于来说明垃圾收集的工作方式，但似乎从未被应用于任何一种JVM。
- 它的依据思想是：对任何“活”的对象，一定能最终追溯到其存活在堆栈和静态存储区之中的引用，这个引用链条可能会穿过数个对象层次。在这种方式下，JVM将采用一种*自适应*的垃圾回收技术。实现取决于不同的JVM的实现。
- “自适应的、分代的、停止-复制、标记-清扫”式垃圾回收器。

## 构造器初始化

### 初始化顺序

> **在类的内部，变量定义的先后决定了初始化的顺序，即使变量定义散布于方法定义之间，它们仍旧会在任何方法（包括构造器）被调用之前得到初始化。**


----------


### 静态数据初始化

> 书中写到*构造器实际上也是静态方法*，似乎是有点问题的。
以下测试代码和解释出自-> [实例构造器是不是静态方法？](http://rednaxelafx.iteye.com/blog/652719)
 举个例子，下面的Java代码
```java
    public class ConstructorDemo {  
        private int value;  
          
        public ConstructorDemo(int i, Object o) {  
            this.value = i;  
        }  
          
        public static void main(String[] args) {  
            ConstructorDemo demo = new ConstructorDemo(2, args);  
        }  
    }  

```
被编译为class文件后，实例构造器与main()方法的内容分别为： 
```
    public ConstructorDemo(int, java.lang.Object);  
      Code:  
       Stack=2, Locals=3, Args_size=3  
       0:   aload_0  
       1:   invokespecial   #1; //Method java/lang/Object."<init>":()V  
       4:   aload_0  
       5:   iload_1  
       6:   putfield        #2; //Field value:I  
       9:   return  
      
    public static void main(java.lang.String[]);  
      Code:  
       Stack=4, Locals=2, Args_size=1  
       0:   new     #3; //class ConstructorDemo  
       3:   dup  
       4:   iconst_2  
       5:   aload_0  
       6:   invokespecial   #4; //Method "<init>":(ILjava/lang/Object;)V  
       9:   astore_1  
       10:  return  
```
先从main()方法开始看。
第一条指令是new，用于创建出ConstructorDemo类型的一个空对象，执行过后指向该对象的引用被压到操作数栈上。
第二条指令是dup，将操作数栈顶的值复制一份压回到栈顶；其中dup出来的一份用于作为隐式参数传到实例构造器里去（对应后面的invokespecial），原本的一份用于保存到局部变量去（对应后面的astore_1）。
第三条指令是iconst_2，将常量2压到操作数栈上，作为ConstructorDemo实例构造器的第一个显式参数。
第四条指令是aload_0，将main()方法的参数args作为ConstructorDemo实例构造器的第二个显式参数。
第五条指令是invokespecial，调用ConstructorDemo实例构造器。再次留意，前面已经传了三个参数，分别是new出来的实例的引用、常量2与main()的参数args。该指令执行过后，操作数栈顶就只剩下dup前通过new得到的引用。
第6条指令是astore_1，将操作数栈顶的引用保存到局部变量1中。执行过后操作数栈空了。
最后一条指令是return，结束main()方法的执行并返回。

然后从ConstructorDemo的实例构造器来看。
第一条指令是aload_0，将第一个参数（不管是隐式还是显式参数）压到操作数栈上。从main()的调用序列可以看到第一个参数是刚new出来的对象实例的引用，对这个构造器来说也就是“this”。
第二条指令是invokespecial，调用Object的实例构造器。前一条指令的“this”就是这个调用的参数。执行过后操作数栈就空了。
第三条指令又是aload_0，再次将“this”压到操作数栈上。
第四条指令是iload_1，将第二个参数压到操作数栈上，也就是i。
第五条指令是putfield，将i赋值给this.value。执行过后操作数栈又空了。
最后一条指令是return，结束该实例构造器的执行并返回。

这个例子的注意点在于：
1、Java的实例构造器只负责初始化，不负责创建对象；Java虚拟机的字节码指令的设计也反映了这一点，有一个new指令专门用于创建对象实例，而调用实例构造器则使用invokespecial指令。
2、“this”是作为实例构造器的第一个实际参数传入的。 

> Java虚拟机规范第二版中定义了四种不同的字节码指令来处理Java程序中不同种类的方法的调用：
> · invokestatic - 用于调用类（静态）方法 
> · invokespecial - 用于调用实例方法，特化于super方法调用、private方法调用与构造器调用 
> · invokevirtual - 用于调用一般实例方法（包括声明为final但不为private的实例方法） 
> · invokeinterface - 用于调用接口方法 


----------


### 可变参数列表
```java
	static void print(Object...objects){
		for (Object object : objects) {
			System.out.println(object);
		}
	}
	static void print(int i ,Integer...integers){
		for (Object integer : integers) {
			System.out.println(integer);
		}
	}
```

> 你应该总是只在重载方法的一个版本上使用可变参数列表，或者压根就不用它。


----------

### 枚举

枚举类型的实例是常量，因此惯例它们都用大写字母表示(有多个单词用下划线隔开)，为了使用enum，需要创建一个该类型的引用，并将其赋值给某个实例`DemoEnum d = DemoEnum.DEMO`,创建enum时编译器会自动添加一些有用的特性，如，toString()，ordinal()<表示某个特定enum常量的声明顺序>，static values()<按照enum常量声明顺序生成数组>。
**事实上enum确实是类，并且具有自己的方法。它与switch语句是绝佳的组合**