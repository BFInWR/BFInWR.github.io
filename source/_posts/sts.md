---
title: STS与SpringBoot
date: 2019-08-17 16:52:00
categories: "Java"
tags:
	- Java
	- STS
	- SpringBoot

---
## STS
**STS(Spring Tool Suite™)**Spring工具套件是一个基于Eclipse的开发环境，用于开发Spring应用程序。它提供了一个现成的环境以实现、调试、运行和部署Spring应用程序，包括Pivotal tc Server、Pivotal Cloud Foundry、Git、Maven、AspectJ的集成，并且位于最新的Eclipse版本之上。

Spring工具套件包括Pivotal tc Server的开发者版本，它是Apache Tomcat的下拉替换，为Spring进行了优化。通过它的Spring Insight控制台，tc Server Developer Edition提供了应用程序性能度量的图形化实时视图，允许开发人员从他们的桌面识别和诊断问题。

Spring工具套件支持以本地、虚拟和基于云的服务器为目标的应用程序。它可免费用于开发和内部业务操作，没有时间限制，完全开放源代码，并根据Eclipse公共许可证条款进行许可。

对于Spring Boot有着很好的支持

**实际用着感受而言 外貌和eclipse一模一样 要稍微好看点 ， 但是用着没有eclipse流畅 错误提示没有intellJ好 可以说是基本没有 电脑稍微差点及其容易未响应 ，eclipse很少 intellJ几乎没有 **
<!-- more -->
## Spring Boot介绍
- 目标：简单，快速开发Spring框架项目
- 特性1：自动配置（无需xml配置，通过jar依赖自动识别）
- 特性2：通过启步（Starter）依赖集成第三方库，开箱即用
- 特性3：内嵌Servlet容器，无需war包部署
- 特性4：内置健康检测、度量指标功能
- 特性5：提供all-in-one打包插件

### Spring Boot两种创建方式
- 方式一：使用[Spring Initiallizr创建](https://start.spring.io/)
- 方式二：使用Spring Tool Suite等IDE创建

####引入Spring Boot
- 方式一：pom继承spring-boot-starter-parent（当需要修改其中的依赖管理版本号时，可以直接覆盖或者修改Properties版本号变量）
- 方式二：pom引入spring-boot-dependencies（这种模式修改Properties版本号是不起作用的，只能覆盖）

#### 引入内嵌容器
- 引入tomcat：直接引入spring-boot-starter-web，默认tomcat
- 引入jetty：还需添加spring-boot-starter-jetty，并移除starter-tomcat
[Use Jetty instead of Tomcat](https://docs.spring.io/spring-boot/docs/1.5.15.RELEASE/reference/htmlsingle/#howto-use-jetty-instead-of-tomcat)

####内嵌容器优势
- 减少外部容器依赖，可移植性高
- 易测试易部署
- Spring Boot提供了可插拔的内嵌容器方案

## 数据访问层搭建-整合MyBatis
- 方式一：通过xml配置
- 1：dataSource配置
- 2：创建SqlSessionFactoryBean，MapperSacnnerConfigurer bean
- 3：创建mybatis配置文件，mapper接口类及sql映射文件

- 方式二：通过Starter
- 1：引入mybatis Starter
- 2：application.properties添加spring mybatis和datasource配置
- 3：创建mybatis配置文件，mapper接口类及sql映射文件

数据传输层需要配置在和启动类在同一层级添加@Mapper 才会被自动扫描到不然就只有用在启动类上面使用@MapperScan配置

### 数据库连接池 — Druid
SpringBoot 默认的使用Tomcat连接池：tomcat-jdbc 

[**Druid**](https://github.com/alibaba/druid/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98)
> Druid是一个JDBC组件库，包括数据库连接池、SQL Parser等组件。DruidDataSource是最好的数据库连接池。

#### Druid 1.1.10

```xml
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>druid-spring-boot-starter</artifactId>
   <version>${druid.version}</version>
</dependency>

<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>druid</artifactId>
   <version>${druid.version}</version>
</dependency>
```
配置
```properties
server.port=8080

#Druid配置
spring.datasource.druid.url=jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8&useSSL=false
spring.datasource.druid.username=root
spring.datasource.druid.password=123

#连接池配置(通常来说，只需要修改initialSize、minIdle、maxActive
#如果用Oracle，则把poolPreparedStatements配置为true，mysql可以配置为false。
#分库分表较多的数据库，建议配置为false。removeabandoned不建议在生产环境中打开如果用SQL Server，建议追加配置)
#初始化连接大小
spring.datasource.druid.initial-size=8
#最大连接数
spring.datasource.druid.max-active=30
#最小连接数
spring.datasource.druid.min-idle=5

#一般情况下 Druid以上配置足以

#启用ConfigFilter
spring.datasource.druid.filter.config.enabled=true

#下面两个配置可有可无 默认也是这个配置
#配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
spring.datasource.druid.time-between-eviction-runs-millis=60000
#配置一个连接在池中最小生存的时间，单位是毫秒
spring.datasource.druid.min-evictable-idle-time-millis=300000

#获取连接的最大等待时间
spring.datasource.druid.max-wait=10000

#解决mysql提出超过8小时空闲的连接的问题 主动定期验证内存里面的连接是否有效 用来检测连接是否有效的sql，要求是一个查询语句，常用select 'x'
spring.datasource.druid.validation-query=SELECT 'x'
spring.datasource.druid.test-on-borrow=false
spring.datasource.druid.test-on-return=false
spring.datasource.druid.test-while-idle=true

#打开PSCache，并且指定每个连接上PSCache的大小
spring.datasource.druid.pool-prepared-statements=true
spring.datasource.druid.max-pool-prepared-statement-per-connection-size=20

#Druid内置提供一个StatFilter，用于统计监控信息。
#DruidFilter慢SQL追查		1ms上的	是否打印	是否合并
spring.datasource.druid.filter.stat.enabled=true
spring.datasource.druid.filter.stat.slow-sql-millis=1
spring.datasource.druid.filter.stat.log-slow-sql=true
spring.datasource.druid.filter.stat.merge-sql=true

#监控配置
# WebStatFilter配置，说明请参考Druid Wiki，配置_配置WebStatFilter
#是否启用StatFilter默认值true
spring.datasource.druid.web-stat-filter.enabled=true
spring.datasource.druid.web-stat-filter.url-pattern=/*
spring.datasource.druid.web-stat-filter.exclusions=*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*
spring.datasource.druid.web-stat-filter.session-stat-enable=false
spring.datasource.druid.web-stat-filter.session-stat-max-count=1000
spring.datasource.druid.web-stat-filter.principal-session-name=admin
spring.datasource.druid.web-stat-filter.principal-cookie-name=admin
spring.datasource.druid.web-stat-filter.profile-enable=true
#Druid内置提供了一个StatViewServlet用于展示Druid的统计信息。
#StatViewServlet的用途包括：1.提供监控信息展示的html页面2.提供监控信息的JSON API
#是否启用StatViewServlet默认值true	映射地址/druid
spring.datasource.druid.stat-view-servlet.enabled=true
spring.datasource.druid.stat-view-servlet.url-pattern=/druid/*
```
**若是报错请先检查druid jar包是否正常 ，可能存在版本冲突，导致maven没有导入完整的druid jar包，但是却没有报错**

## 整合freemarker步骤
- 方式一 通过xml配置
- 1：pom引入freemarker库
- 2：创建freemarkerConfigurer、viewResolver bean
- 3：编写模板引擎文件

- 方式二 通过Starter
- 1：引入freemarker starter
- 2：application.properties添加freemarker配置
- 3：编写模板引擎文件

### Freemarker结构化布局
- 抽取header、footer、nav、js、分页
- 页面中引入header、footer
- 编写页面中自定义的部分.

## 起步依赖（Starter）

一：
> spring-boot-starter
> ——spring-boot
> ————spring-core
> ————spring-context
> ——spring-boot-autoconfigure（包含大量自动配置） 
> ——spring-boot-starter-logging

二：
> mybatis-spring-boot-starter
> ——mybatis
> ——spring-boot-starter-jdbc
> ——mybatis-spring
> ——mybatis-spring-boot-autoconfigure

### 自动配置

> @SpringApplicationBoot -> @EnableAutoConfiguration -> SpringFactoriesLoader(classpath:/META-INF/spring.factories)
> @EnableXxxx

**Java Config中有很多注解**
configuration 代表它是一个Java Config
会有一些条件注解
ConditionalOnMissingBean 如果不存在这个Bean则创建 当用户创建时这个Bean就不生效
@ConditionalOnProperty	如果开启某个配置则这个Bean则创建
@ConditionalOnClass		在某些类存在的情况下 才去加载

### 自定义自动配置
列子：
访问网站都是以Http访问的，就需要HttpClient 单例就可以 是一个SpringBean的存在

```java
@Configuration
@ConditionalOnClass({HttpClient.class})
//将HttpClientProperties 作为一个Bean引入进来
@EnableConfigurationProperties(HttpClientProperties.class)
public class HttpClientAutoConfiguration {
	
	private final HttpClientProperties properties;
	
	public HttpClientAutoConfiguration(HttpClientProperties properties){
		this.properties = properties;
	}
	
	/**
	 * httpClient bean 的定义
	 * @return
	 */
	@Bean
	@ConditionalOnMissingBean(HttpClient.class)
	public HttpClient httpClient(){
		RequestConfig requestConfig = RequestConfig.custom()
				.setConnectTimeout(properties.getConnectTimeOut())
				.setSocketTimeout(properties.getSocketTimeOut())
				.build();
		HttpClient client = HttpClientBuilder.create()
				.setDefaultRequestConfig(requestConfig)
				.setUserAgent(properties.getAgent())
				.setMaxConnPerRoute(properties.getMaxConnPerRoute())
				.setMaxConnTotal(properties.getMaxConnTotaol())
				.setConnectionReuseStrategy(new NoConnectionReuseStrategy())
				.build();//setConnectionReuseStrategy 不进行重试 可以在业务中进行 不在http中
		
		return client;
	}
}

```

就是需要启动给application.properties这个文件,提供给用户 进行配置的
```java
//声明为一个SpringBoot的配置文件
@ConfigurationProperties(prefix="spring.httpclient")
public class HttpClientProperties {
	
	private Integer connectTimeOut = 1000;	//连接超时 1s
	private Integer socketTimeOut = 10000;	//读超时 10s
	
	private String agent = "agent";
	private Integer maxConnPerRoute = 10; //每个IP最大连接数
	private Integer maxConnTotaol = 50;	//总连接数
....getter
....setter
}
```

因为上面两个类是在root Package下的子Package，所以加了@configuration 都会被自动扫描到也会自动加载进来，当不是子Package下的时候，就会报错找不到HttpClient这个Bean，这个时候可以使用META-INF 进行配置
```xml
resources
-- META-INF
-- spring.factories
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
xxx.xxx.xxx.HttpClientAutoConfiguration
```

另一种方法是使用注解
```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import org.springframework.context.annotation.Import;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Import(HttpClientAutoConfiguration.class)
public @interface EnableHttpClient {
	
}

//然后在启动类上加上注解 @EnableHttpClient 就行了
```

## maven 多module的实现

> 好处：让我们代码层次逻辑更加清晰，增强代码的内聚性，可以使多人负责不同的module，增加协同开发的效率
> 它并不意味着分布式，最终还是要打成一个jar包，多module通过 maven依赖最终会集成到一起

拆分例子
- house-biz：负责数据访问层以及业务的实现
- house-common：负责定义数据模型以及公共代码
- house-web：负责定义启动类、controller、模板引擎

**工作组**
[使用和建立](https://jingyan.baidu.com/article/f0e83a2563c8eb22e591011b.html)

intellj 直接创建maven项目 -> 在这个项目下 右键 new moudle 




