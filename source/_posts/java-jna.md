---
title: 项目笔记 - JNA
date: 2018-08-17 16:52:00
categories: "Java"
tags:
	- Java
	- JNA

---
## JNA

**JNA jar包**
![Alt text](java-jna/jna-3.2.5.jar.zip)

[4.5.2 点击直接下载的链接地址](http://repo1.maven.org/maven2/net/java/dev/jna/jna/4.5.2/jna-4.5.2.jar)

[JNA Examples](https://www.eshayne.com/jnaex/index.html)

[JNA 英文文档](http://java-native-access.github.io/jna/4.5.2/javadoc/)

[JNA 英文概述](http://java-native-access.github.io/jna/4.5.2/javadoc/overview-summary.html#overview_description)

[stackoverflow-JNA](https://stackoverflow.com/questions/tagged/jna)

[官网入门教程](https://github.com/java-native-access/jna/blob/master/www/GettingStarted.md)

> **Java Native Access（JNA）**
> JNA为Java程序提供了对本机共享库的轻松访问，而无需编写除Java代码之外的任何内容 - 不需要JNI或本机代码。此功能可与Windows的Platform / Invoke和Python的ctypes相媲美。

> JNA允许您使用自然Java方法调用直接调用本机函数。Java调用看起来就像本机代码中的调用一样。大多数呼叫不需要特殊处理或配置; 不需要样板或生成的代码。

> JNA使用小型JNI库存根来动态调用本机代码。开发人员使用Java接口来描述目标本机库中的函数和结构。这使得利用本机平台功能变得非常容易，而不会产生为多个平台配置和构建JNI代码的高额开销。

>虽然对性能给予了极大的关注，但正确性和易用性优先考虑。

>此外，JNA还包括一个平台库，其中已经映射了许多本机函数，以及一组简化本机访问的实用程序接口。
>—— 以上Google翻译


### 官网小实例

```java
package com.sun.jna.examples;

import com.sun.jna.Library;
import com.sun.jna.Native;
import com.sun.jna.Platform;

public class HelloWorld {

    // 这是标准的，稳定的映射方式，它支持广泛的
    // 自定义和Java到本机类型的映射

    public interface CLibrary extends Library {
        CLibrary INSTANCE = (CLibrary)
            Native.load((Platform.isWindows() ? "msvcrt" : "c"),
                                CLibrary.class);

        void printf(String format, Object... args);
    }

    public static void main(String[] args) {
        CLibrary.INSTANCE.printf("Hello, World\n");
        for (int i=0;i < args.length;i++) {
            CLibrary.INSTANCE.printf("Argument %d: %s\n", i, args[i]);
        }
    }
}
```

### 官网使用详解

1. 使您的目标库可用于Java程序。让你的类路径中的本地库，该路径下{OS}-{ARCH}/{LIBRARY}，这里{OS}-{ARCH}是JNA对本地库规范前缀（如win32-x86，linux-amd64或darwin）。如果资源在jar文件中，则在加载时将自动提取。（PS:不多的话直接放在项目根目录下就好了。)
2. 在此接口中，使用Native.load（Class）方法定义本机库的实例，提供先前定义的本机库接口。
```java
Kernel32 INSTANCE = (Kernel32)
    Native.load("kernel32", Kernel32.class);
    
// 可选：在异步块中封装对本地库的每个调用，每次限制本地调用一个
Kernel32 SYNC_INSTANCE = (Kernel32)
    Native.synchronizedLibrary(INSTANCE);
```
3. 该INSTANCE变量用于方便地重用库的单个实例。或者，您可以将库加载到局部变量中，以便在超出范围时可用于垃圾回收。可以提供选项映射作为加载以定制库行为的第三个参数; 这SYNC_INSTANCE也是可选的; 如果您需要确保您的本机库一次只能调用一次，请使用它。
```java
@FieldOrder({ "wYear", "wMonth", "wDayOfWeek", "wDay", "wHour", "wMinute", "wSecond", "wMilliseconds" })
public static class SYSTEMTIME extends Structure {
    public short wYear;
    public short wMonth;
    public short wDayOfWeek;
    public short wDay;
    public short wHour;
    public short wMinute;
    public short wSecond;
    public short wMilliseconds;
}

void GetSystemTime(SYSTEMTIME result);
...
Kernel32 lib = Kernel32.INSTANCE;
SYSTEMTIME time = new SYSTEMTIME();
lib.GetSystemTime(time);

System.out.println("Today's integer value is " + time.wDay);
```

### 我的实例操作

**DemoDll .dll是一个C++写的dll文件，放在项目根目录下就好和src同级**

```java
import com.sun.jna.Library;
import com.sun.jna.Native;
import com.sun.jna.Structure;
import com.sun.jna.ptr.IntByReference;

public final class Demo {
	protected interface CLibrary extends Library{
		CLibrary INSTANCE = (CLibrary)
			Native.loadLibrary("DemoDll",CLibrary.class); 
		
		// 对应C++中的 HRESULT GetStauts(WORD *wStatus)
		// 传入的是基本类型的引用
		int GetStauts(IntByReferce wStatus);

		// 对应C++中的 BOOL OpenSTP(COM com) 传入的是对象引用
		Boolean Open(COM.ByReference struct);

		public static class COM extends Structure{
			public short fwCom;
			public int fwBaud;
			public byte fwByteSize;
			...
			public static class ByReference extends COM
				implements Structure.ByReference{}
		}
	}
```

对应上面的COM
```
typedef struct _com
	{
	   short fwCom; 
	   WORD fwBaud; 
	   BYTE fwByteSize; 
	} COM, *LPCOM;
```

```java
...main...
final COM.ByReference com = new COM.ByReference();
Boolean bool = CLibrary.INSTANCE.Open(com);

final IntByReference wStatus = new IntByReference();
int status =  CLibrary.INSTANCE.GetStauts(wStatus);
```