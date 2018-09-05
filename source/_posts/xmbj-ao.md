---
title: 在桌面应用开发项目中走过的凹
date: 2018-09-05 10:52:00
categories: "Java"
tags:
	- Java
	- 桌面应用程序
	- 项目笔记

---

## 前言
此文章结合了在项目开发过程中遇到并解决的一些问题和难点，以及一些比较好用的model，引以启发。

<!--more-->

----------
1. junit 单元测试 不支持多线程测试，当主线程结束时整个进程就会结束。若是想要执行子线程那么 可以使用 ` Thread.sleep() ` 对主线程进行睡眠，`使用 子线程.join()`可以让该子线程先执行完再执行主线程，但是这种方法只适用于 线程使用`start()`方法启动的。


----------


2. 不用Servlet容器去启动Spring，在main方法里面启动Spring
```java
ApplicationContext ac = new ClassPathXmlApplicationContext("classpath:spring/spring-dao.xml", "classpath:spring/spring-service.xml" );

//获取Bean
ac.getBean(serviceName);
```

----------


3. 对JButton大小的设置因为JButen是属于小器件类型的，所以一般的setSize不能对其惊醒大小的设置，所以一般我们用`button.setPreferredSize(new Dimension(30,30));`,对于其它组件如 JMenuBar 也同样适用。

----------

4. `public void setUndecorated(boolean undecorated)`禁用或启用此框架的外部边框。默认是启用，使用这个方法时要注意放置的位置 需要在组件显示之前设置。一般设置在init方法中的前几行。

----------


5. 在使用全局字体的过程中发现，有些字体使用了样式，而有些却没有使用，我想这是因为我把全局字体放到了子线程中加载的原因，于是我把它放到主线程Main方法中，果然就行了。

----------

6. 若是使用了全局字体，一样的可以在组件中使用设置字体方法`.setFont()`去覆盖全局效果。

----------


7. JPanel 背景图设置 
```java
 panel = new JPanel(){
            @Override
            protected void paintComponent(Graphics graphics) {
                ImageIcon icon = new ImageIcon("images\\background.jpg");
                graphics.drawImage(icon.getImage(), 0, 0, frame.getWidth(), frame.getHeight(), frame);
            }
        };
```

----------


8. JButton 带文字背景图设置 
```java
JButton showDocButton = new JButton("预览");
showDocButton.setIcon(new ImageIcon("images\\show.png"));
showDocButton.setHorizontalTextPosition(SwingConstants.CENTER);//文字居中
showDocButton.setContentAreaFilled(false); //背景透明
showDocButton.setBorderPainted(false);		//无边框

public void setContentAreaFilled(boolean b)
设置contentAreaFilled属性。 如果true这个按钮将画出内容区域。 如果您希望有一个透明按钮，例如仅图标按钮，那么您应该将其设置为false 。 不要使用setOpaque(false) 。
```

----------


9. 使用MigLayout布局，这种布局非常适合充当表格布局，而且里面的每列每行的大小都可以调，可以配合scrollPane使用
[pdf官方文档](http://www.miglayout.com/QuickStart.pdf)
```xml
<dependency>
    <groupId>com.miglayout</groupId>
    <artifactId>miglayout-swing</artifactId>
    <version>5.2</version>
</dependency>
```
```java
panel.setLayout(new MigLayout("", "[30][40][50][60]", "[50]"));
//布局代表4列3行 (Layout Constraints,Column constraints,Row constraints)

JButton Button_1 = new JButton("New button");
panel.add(Button_1 , "cell 1 0,alignx center");
//把这个按钮放在第1列0行 位于这个区域的中心
```

----------

10. 日志模板
**注意log4j.properties 需要放到classpath一级下（resources）才能生效不能放在其它文件夹中**
```xml
### 设置###
log4j.rootLogger = info,stdout,D,E

### 输出信息到控制抬 ###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss,SSS} method:%l%n%m%n

### 输出DEBUG 级别以上的日志到=//logs/error.log ###
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = logs\\log.log
log4j.appender.D.Append = true
log4j.appender.D.Threshold = INFO
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n

### 输出ERROR 级别以上的日志到=//logs/error.log ###
log4j.appender.E = org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.File = logs\\error.log
log4j.appender.E.Append = true
log4j.appender.E.Threshold = ERROR
log4j.appender.E.layout = org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
```