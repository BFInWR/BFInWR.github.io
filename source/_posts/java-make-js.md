---
title: Java中调用JS
date: 2020-4-17 16:52:00
categories: "Java"
tags:
	- Java
	- JS

---
## Java中调用JS
JDK自带的类可以实现调用JS的功能，可以实现执行字符串中的运算公式的功能。

<!--more-->
```java
public class runString {
 
	static ScriptEngine jse = new ScriptEngineManager().getEngineByName("JavaScript");
 
	/**
	 * @param args
	 */
	public static void main(String[] args) {
		String strs = "1+1*2+(10-(2*(5-3)*(2-1))-4)+10/(5-0)";
		try {
			System.out.println(jse.eval(strs));
		} catch (Exception t) {
		}
	}
}
```
注意：如果公式中存在变量作为运算的元素的话，可以借鉴使用replaceAll()方法将相应的变量替换成实际的数值。如果是多个变量可以利用循环遍历的方式来解决。

例子：jse.eval("1+b".replaceAll("b", b.toString()))；