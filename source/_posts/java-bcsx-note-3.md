---
title: Java编程思想笔记（三）
date: 2018-08-02 16:52:00
categories: "Java"
tags:
	- Java
	- 书籍笔记
---

## 持有对象

```java
Collection<Integer> c1 = new ArrayList<Integer>(Arrays.asList(1,2,3,4)); 
c1.addAll(Arrays.asList(5,6));
Collections.addAll(c1, 7,8); //这种方式比Arrays.asList快的多

List<Integer> l = Arrays.asList(1,2,3,4); 
//底层是数组 不是ArrayList 没有add方法
```

> 与HashSet一样，HashMap也提供了最快的查找技术，也没有按照任何明显的顺序来保存其元素。TreeMap按照比较结果升序保存键，而LinkedHashMap则按照插入顺序保存键，同时保留了HashMap的查询速度。
<!--more-->
### List

> ArrayList 访问快，读写较慢
> LinkedList 访问较慢，读写快，特征集比ArrayList大。

> **方法**
> 1. 当使用`remove()`或者`removeAll()`方法使用参数是值时，会依赖于equals()方法
> 2. `toArray()`方法可以将任意一个`Connlection`转换为一个数组，无参数将返回一个`Object[]`,如果你传递目标类型的数据，它将产生指定类型的数据，参数大小不会限制数组长度。
```java
Collection<Integer> c1 = new ArrayList<Integer>(Arrays.asList(1,2,3,4)); 
Integer[] i =  c1.toArray(new Integer[0]);
//如果不加参数 不能强转成Integer[] 只能返回Object[] 
System.out.println(i.length); //4
```

> **LinkedList**有使其作为 栈、队列、双端队列 的方法

### 迭代器

> 迭代器是一个对象，它的工作是遍历并选择系列中对象，客户端程序员不必指定或关系该系列的底层结构。此外，迭代器常常被成为**轻量级对象：创建它的代价小**。因此，经常可以见到对迭代器有些奇怪的限制，例如，Java的**Iterator**只能单向移动，这个**Iterator**只能用于：
> 1. 使用方法**iterator()**要求返回一个Iterator。Iterator将准备好返回序列的第一个元素。
> 2. 使用**next()**获得序列中的下一个元素。
> 3. 使用**hasNext()**检查序列中是否还有元素。
> 4. 使用**remove()**将迭代器新近返回的元素删除。

#### ListIterator

> **ListIterator**是一个更加强大的**Iteraor**的子类型，它只能用于各种**List**类的访问。尽管**Iterator**只能向前移动，但是**ListIterator**可以双向移动。它还可以产生相对于迭代器在列表中指向的当前位置的前一个和后一个元素的索引，并且可以使用**set()**方法替换它访问的最后一个元素。
```java
List<String> list = new ArrayList<String>(Arrays.asList("a","b","c")); 
	ListIterator<String> it = list.listIterator();
	while(it.hasNext()){
		System.out.println(it.next()+"-"+it.nextIndex()+"-
		"+it.previousIndex());
	}
/*  a-1-0
	b-2-1
	c-3-2  */
```

### 栈

> “栈”通常是指“后进先出”(LIFO)
```java
//使用LinkedList能够直接实现栈的所有功能方法，尽管已经有了java.util.Stack。
//但是LinkedList可以产生更好的Stack
class Stack<T>{
	private LinkedList<T> storage = new LinkedList<T>();
	public void push(T v){storage.addFirst(v);} //存元素 存在第一位
	public T peek(){return storage.getFirst();}	//获取元素 第一位 栈顶元素
	public T pop(){return storage.removeFirst();} //删除元素  删除第一位
}
```

### Set

> Set不保存重复的元素。查找是**Set**中最重要的操作，因此你通常都会选择一个HashSet的实现，它专门对快速查找进行了优化。
> **Set具有与Collection完全一样的接口，因此没有任何额外的功能，只是行为不同。Set是基于对象的值来确定归属性的**
> **HashSet**使用的是散列函数
> **TreeSet**使用的是红-黑数
> **LinkedHashSet**因为查询速度原因也使用了散列，链表维护插入顺序
> //书中232页写的LinkedHashList我并在jdk8中看见，只有LinkedHashSet和LinkedHashMap，应该是笔误

### Map

> 将对象映射到其它对象的能力是一种解决编程问题的杀手锏。

```java
//统计随机数的次数
Random rand = new Random(47);
Map<Integer, Integer> m = new HashMap<Integer,Integer>();
for (int i = 0; i < 20; i++) {
	int r = rand.nextInt(20);
	m.put(r, m.get(r) == null ? 1: m.get(r)+1);
}
System.out.println(m.toString());
```

### Queue

> 队列是一个典型的**先进先出**(FIFO)的容器。
> LinkedList提供了方法以支持队列的行为，并且它实现了Queue接口，因此LinkedList可以用作Queue的一种实现。`Queue queue = new LinkedList()`

#### PriorityQueue 优先级队列

> 它可以确保你调用 peek() 、poll()、remove() 方法时，获取的元素将是优先级最高的。

### foreach 与 迭代器

> **Iterator**接口被foreach用来在序列中移动。因此如果你创建了任何实现**Iterator**的类，都可以将它用于foreach语句中。
> 大量的类都是**Iterator**的类型，包括所有的Collection类(但是不包括各种Map，但是由entrySet()产生的Map.Entry的元素构成的是Set，keySet()也是返回Set)。
> 数组到 **Iterator** 不会把基本类型自动转换成包装类。必须手动转换。

### 总结

1. 容器不能持有基本类型，但是自动包装机制会仔细地执行基本类型到到容器中所持有的包装类型之间的双向转换。
2. 新程序中不应该使用过时的Vector、Hashtable、Stack.
3. List可以生成**listIterator**也可以生成**Iterator**
4. 除了TreeSet之外的所有Set都拥有与Colleaction完全一样的接口。

----------

## 字符串
```java
public class Concatenation{
	public static void main(String[] args){
		String s1 = "asdf";
		String s2 = "qwe"+s1+"ert"+47;
		System.out.println(s2);
	}
}
//qweasdfert47
```
```
//反编译如下 简化部分注解
C:\Users\Administrator\Desktop>javap -c Concatenation
Compiled from "Concatenation.java"
public class Concatenation {
  public Concatenation();
    Code:
       0: aload_0
       1: invokespecial #1   // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: ldc           #2 // String asdf
       2: astore_1
       3: new           #3 // class StringBuilder
       6: dup
       7: invokespecial #4 // Method StringBuilder."<init>":()V
      10: ldc           #5 // String qwe
      12: invokevirtual #6 //Method StringBuilder.append:(LString;)
      15: aload_1
      16: invokevirtual #6 // Method StringBuilder.append:(LString;)
      19: ldc           #7 // String ert
      21: invokevirtual #6 // Method StringBuilder.append:(LString;)
      24: bipush        47
      26: invokevirtual #8 // Method StringBuilder.append:(I)
      29: invokevirtual #9  // Method StringBuilder.toString:()
      32: astore_2
      33: getstatic     #10 // Field System.out:Ljava/io/PrintStream;
      36: aload_2
      37: invokevirtual #11 // Method PrintStream.println:(LString;)V
      40: return
}
```
从反编译可以看到，编译器自动引入了java.lang.StringBuilder类。因为它更加高效。
编译器创建了StringBuilder对象，用以构造String，并为每个字符串调用一次1次StringBuilder的append()方法，总计四次。最后调用toString()方法生成结果，并存为s2(使用的命令是astore_2)

**但是不要一味地想着编译器帮你优化就是最好的**
```java
	public String func1(String[] str){
		String result = "";
		for(int i =0;i<str.length;i++){
			result +=str[i];
		}
		return result;
	}
	
	public String func2(String[] str){
		StringBuilder result = new StringBuilder();
		for(int i =0;i<str.length;i++){
			result.append(str[i]);
		}
		return result.toString;
	}
```
反编译如下
```
 public static java.lang.String func1(java.lang.String[]);
    Code:
       0: ldc           #10   // String
       2: astore_1
       3: iconst_0
       4: istore_2
       5: iload_2
       6: aload_0
       7: arraylength
       8: if_icmpge     38   //i<str.length 比较false时跳转到38行
      11: new           #11  // class StringBuilder 
      14: dup
      15: invokespecial #12  // Method StringBuilder."<init>":()V
      18: aload_1
      19: invokevirtual #13 // Method StringBuilder.append:(LString;)
      22: aload_0
      23: iload_2
      24: aaload
      25: invokevirtual #13 // Method StringBuilder.append:(LString;)
      28: invokevirtual #14 // Method jStringBuilder.toString:()LString;
      31: astore_1
      32: iinc          2, 1
      35: goto          5   //回到第五行 构成一个循环
      38: aload_1
      39: areturn

  public static java.lang.String func2(java.lang.String[]);
    Code:
       0: new           #11 // class StringBuilder
       3: dup
       4: invokespecial #12 // Method StringBuilder."<init>":()V
       7: astore_1
       8: iconst_0
       9: istore_2
      10: iload_2
      11: aload_0
      12: arraylength
      13: if_icmpge     30
      16: aload_1
      17: aload_0
      18: iload_2
      19: aaload
      20: invokevirtual #13 // Method StringBuilder.append:(String;)
      23: pop
      24: iinc          2, 1
      27: goto          10 //回到第10行，与13行构成一个循环 
      30: aload_1
      31: invokevirtual #14 // Method StringBuilder.toString:()LString;
      34: areturn
}
```
所以可以看到func1 每次循环都会新建一个StringBuilder，而func2只会新建一个StringBuilder，你还可以在代码中显示的指定其大小，以避免多次重新分配缓冲。（缓冲区(buffer)，它是内存空间的一部分。也就是说，在内存空间中预留了一定的存储空间，这些存储空间用来缓冲输入或输出的数据，这部分预留的空间就叫做缓冲区，显然缓冲区是具有一定大小的。用以缓解冲击。）

如果你向走捷径，例如`append(a+":"+c)`，那编译器就会为你创建另一个StringBuilder对象去处理括号内的字符串操作。

**Java中每个类从根本上都是继承自Object。因此容器类都有toString()方法，并且重写该方法。所以在重写toString方法时一定要注意编译器的无意识的递归，当你在toString中使用this操作的时候，当你需要转换成String类型时，编译器会自动调用this.toString()方法**
