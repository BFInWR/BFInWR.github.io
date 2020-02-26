---
title: 错误合集
date: 2019-10-17 15:52:00
categories: "错误合集"
tags:
	- jenkins
	- maven
	- MyBatis
	- Spring

---
## 错误合集


[Mybatis错误：Parameter 'XXX' not found. Available parameters are\[1, 0, param1, param2\]](https://blog.csdn.net/w86440044/article/details/29363067)


[base64 to image](https://blog.csdn.net/luanpeng825485697/article/details/78242083)
[base64 to image](https://www.cnblogs.com/ywlx/p/4544179.html)



[MySQL字段自增自减的SQL语句](https://blog.csdn.net/zhouzme/article/details/18909469)


[idea的xml文件Tag name expected](https://blog.csdn.net/shichuwu/article/details/82561715)
<!--more-->
```
[ERROR] 11:54:46.645 [Tomcat-startStop-1] o.apache.catalina.core.ContainerBase - A child container failed during start
java.util.concurrent.ExecutionException: org.apache.catalina.LifecycleException: Failed to start component [StandardEngine[Tomcat].StandardHost[localhost].TomcatEmbeddedContext[]]
	at java.util.concurrent.FutureTask.report(FutureTask.java:122)
	at java.util.concurrent.FutureTask.get(FutureTask.java:192)
	at org.apache.catalina.core.ContainerBase.startInternal(ContainerBase.java:941)
	at org.apache.catalina.core.StandardHost.startInternal(StandardHost.java:872)
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:150)
	at org.apache.catalina.core.ContainerBase$StartChild.call(ContainerBase.java:1421)
	at org.apache.catalina.core.ContainerBase$StartChild.call(ContainerBase.java:1411)
	at java.util.concurrent.FutureTask.run(FutureTask.java:266)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at java.lang.Thread.run(Thread.java:745)
Caused by: org.apache.catalina.LifecycleException: Failed to start component [StandardEngine[Tomcat].StandardHost[localhost].TomcatEmbeddedContext[]]
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:167)
	... 6 common frames omitted
Caused by: org.apache.catalina.LifecycleException: Failed to start component [Pipeline[StandardEngine[Tomcat].StandardHost[localhost].TomcatEmbeddedContext[]]]
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:167)
	at org.apache.catalina.core.StandardContext.startInternal(StandardContext.java:5166)
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:150)
	... 6 common frames omitted
Caused by: org.apache.catalina.LifecycleException: Failed to start component [org.apache.catalina.authenticator.NonLoginAuthenticator[]]
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:167)
	at org.apache.catalina.core.StandardPipeline.startInternal(StandardPipeline.java:182)
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:150)
	... 8 common frames omitted
Caused by: java.lang.NoSuchMethodError: javax.servlet.ServletContext.getVirtualServerName()Ljava/lang/String;
	at org.apache.catalina.authenticator.AuthenticatorBase.startInternal(AuthenticatorBase.java:1183)
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:150)
	... 10 common frames omitted
```

>关键点在于 ` Caused by: java.lang.NoSuchMethodError: javax.servlet.ServletContext.getVirtualServerName()Ljava/lang/String;`
> 在javax.servlet包中 ServletContext类中的 getVirtualServerName() 没有这样的方法错误，()Lxxx表示方法声明返回值为String类型的方法，但是当你点进方法中去看的时候确实又存在，所以很有可能就是javax.servlet包冲突了，有另外一个javax.servlet包存在但是他没有 getVirtualServerName() 这样的方法，所以需要去看看你导入的其它Jar包是哪个和它冲突了，如果是用的maven 只需使用exclusions即可。
> 比如我的就是axis2-kernel包中的和Tomact中的servlet包冲突了
```xml
<dependency>
   <groupId>org.apache.axis2</groupId>
    <artifactId>axis2-kernel</artifactId>
    <version>1.7.8</version>
    <exclusions>
        <exclusion>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

[mavne工程运行报错：软件包javax.crypto 不存在解决办法](https://blog.csdn.net/wangliutao1/article/details/53997357)

[springboot Jar包 秒部署到Linux服务器运行](https://blog.csdn.net/whh18254122507/article/details/78011713?utm_source=blogxgwz2)

[jenkins部署jar项目、springboot项目部署](https://blog.csdn.net/shenhonglei1234/article/details/71480064?utm_source=blogxgwz1)

[maven 如何引入本地jar包](https://blog.csdn.net/peterkang202/article/details/78577381)

[SpringBoot多模块项目打包方法（jar和war）](https://blog.csdn.net/Dongguabai/article/details/80812335?utm_source=blogxgwz0)

[Spirng boot maven多模块打包踩坑](https://blog.csdn.net/Ser_Bad/article/details/78433340?utm_source=blogxgwz4)

>国家sql
![国家sql](https://github.com/BFInWR/BFInWR.github.io/blob/hexo/source/_posts/resource/error-list-1-geo-master.zip)
