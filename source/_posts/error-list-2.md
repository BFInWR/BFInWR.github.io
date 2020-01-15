---
title: 错误合集2
date: 2019-5-17 16:52:00
categories: "错误合集"
tags:
	- mysql
	- maven
	- linux

---
## 错误合集2

网页效果 需要时间 加载缓慢的时候 注意排除 css 中或者 js中的网址 一帮使用@import 引入网址 

<!--more-->
[enctype="multipart/form-data"表单传值问题](https://blog.csdn.net/tuesdayma/article/details/78773437)

[linux 后台启动java jar(守护线程)](https://blog.csdn.net/weixin_41574643/article/details/79716052)

[maven打可执行的jar包，将配置文件打到外面。pom.xml里面的配置](https://blog.csdn.net/qq_35468610/article/details/81008462)

导入数据库
常用source 命令
进入mysql数据库控制台，如
mysql -u root -p
mysql>use 数据库
然后使用source命令，后面参数为脚本文件(如这里用到的.sql)
mysql>source d:/dbname.sql

[spring-boot 禁用swagger的方法](https://www.jb51.net/article/135075.htm)

[java.lang.NoClassDefFoundError: org/apache/tomcat/util/http/mapper/Mapper](https://stackoverflow.com/questions/22321241/tomcat-mapper-class-not-found)
```java
<dependency>
  <groupId>org.apache.tomcat.embed</groupId>
  <artifactId>tomcat-embed-core</artifactId>
  <version>${embedded.tomcat.version}</version>
</dependency>
```

[maven打包pom文件节点在windows和linux下的格式差异](https://blog.csdn.net/qq_25925973/article/details/79015035)
> ${path.separator}这个分隔符，在Windows系统下是“;”分号，在Linux下是“:”冒号。 
>为了使打包时自动选择，可以使用${path.separator}。

```
The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: [/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib]
```
> [解决Linux下Tomcat由于Tomcat Native问题启动报错](https://blog.csdn.net/zhuyu4839/article/details/13008003)
> linux 安装apr 然后libtcnative-1.so 把这个移到/usr/lib64下

[enctype="multipart/form-data"表单传值问题](https://blog.csdn.net/tuesdayma/article/details/78773437)