---
title: IDEA 上的标红
date: 2019-11-17 16:52:00
categories: "IDEA"
tags:
	- Java
	- 项目笔记
	- IDEA

---
## intellJ 上的标红

1. `spring boot Configuration Annotation Proessor not found in classpath`
解决方案：导入依赖
```xml
<dependency>
   <groupId> org.springframework.boot </groupId>
   <artifactId> spring-boot-configuration-processor </artifactId>
   <optional> true </optional>
</dependency>
```
若是还标红 重启下intellJ就行了

2. `Could not autowire. No beans of 'xxxxMapper' type found`
解决方案：安装 Mybatis Plugin 插件
注意Mybatis Plugin是收费的 另外有一个Free Mybatis Plugin但是没有效果

3. `cannot resolve property under construction in mybatis-mapper.xml`
解决方案：1：输全路径。2 [在modules下spring中添加Application xml](http://blog.canang.com.my/2016/06/03/intellij-idea-cannot-resolve-property-key/)
<!-- more -->
## 错误页面
- 错误页面路径配置 `server.error.path(默认error)`
- 异常处理器来进行日志输出（Spring Boot提供了） 

```java
//Controller 辅助类
ControllerAdvice
public class ErrorHandler{
	@ExceptionHandler(value={Exception.class,RuntimeException.class})
	public String error500(HttpServletRequest request,Exception e){
		logger.error(request.getRequestURL()+" 500")
		return "error/500";
	}
}
```
