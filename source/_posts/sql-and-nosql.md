---
title: 关系型数据库和非关系型数据库
date: 2020-3-5 15:52:00
categories: "数据库"
tags:
	- 数据库
	
---

## 关系型数据库和非关系型数据库
数据库是数据的仓库。
与普通的“数据仓库”不同的是，数据库依据“数据结构”来组织数据，因为“数据结构”，所以我们看到的数据是比较“条理化”的（比如不会跟以前的普通文件存储式存储成一个文件那么不条理化，我们的数据库分成一个个库，分成一个个表，分成一条条记录，这些记录是多么分明）
也因为其“数据结构”式，所以有极高的查找速率（比如B-Tree查找法），（由于专精，可以根据自己的结构特性来快速查找，所以对于数据库的查找会比较快捷；不像普通文件系统的“查找”那么通用）
如果与EXCEL来比的话，能明显的看出数据库的好处，我们能给一个个“字段”添加“约束”（比如约束一列的值不能为空）
数据库与普通的文件系统的主要区别（起因）：数据库能快速查找对应的数据
常说的XX数据库，其实实质上是XX数据库管理系统。数据库管理系统是一个软件，是数据库管理的程序实现。
<!--more-->
***
### 关系型数据库
关系型数据库：指采用了关系模型来组织数据的数据库。
关系模型指的就是二维表格模型，而一个关系型数据库就是由二维表及其之间的联系所组成的一个数据组织。

关系型数据库的优点：
1.容易理解：二维表结构，
2.使用方便：通用的SQL语言使得操作关系型数据库非常**方便**
3.**易于维护：丰富的完整性**(实体完整性、参照完整性和用户定义的完整性)大大减低了数据冗余和数据不一致的概率

关系型数据库存在的问题
1.网站的用户并发性非常高，往往达到每秒上万次读写请求，对于传统关系型数据库来说，**硬盘I/O是一个很大的瓶颈**
2.网站每天产生的数据量是巨大的，对于关系型数据库来说，在一张包含海量数据的表中查询，**效率是非常低**的
3.在基于web的结构当中，数据库是最**难进行横向扩展**的，当一个应用系统的用户量和访问量与日俱增的时候，数据库却没有办法像web server和app server那样简单的通过添加更多的硬件和服务节点来扩展性能和负载能力。当需要对数据库系统进行升级和扩展时，往往需要停机维护和数据迁移。
4.**性能欠佳：**在关系型数据库中，导致性能欠佳的**最主要原因是多表的关联查询**，以及复杂的数据分析类型的复杂SQL报表查询。为了保证数据库的ACID特性，必须尽量按照其要求的范式进行设计，关系型数据库中的表都是存储一个格式化的数据结构。
***
### 非关系型数据库
非关系型数据库：指非关系型的，分布式的，且**一般不保证遵循ACID原则**的数据存储系统。

优点：
键值对存储。
一种数据结构化存储方法的集合。

不足：
只适合存储一些较为简单的数据，对于需要**进行较复杂查询的数据**，关系型数据库显的更为合适。
**不适合持久存储海量数据**

面向高性能并发读写的key-value数据库：redis
面向海量数据访问的面向文档数据库：MongoDB
***
### CAP理论
**NoSQL的基本需求就是支持分布式存储，严格一致性与可用性需要互相取舍**
一个分布式系统**不可能同时满足C(一致性)、A(可用性)、P(分区容错性)三个基本需求**，并且最多只能满足其中的两项。对于一个分布式系统来说，分区容错是基本需求，否则不能称之为分布式系统，因此需要在C和A之间寻求平衡
**C(Consistency)一致性**
一致性是指更新操作成功并返回客户端完成后，所有节点在**同一时间的数据完全一致**。与ACID的C完全不同
**A(Availability)可用性**
可用性是指服务一直可用，而且是正常响应时间。
**P(Partition tolerance)分区容错性**
分区容错性是指分布式系统在遇到某节点或网络分区故障的时候，仍然能够对外提供满足一致性和可用性的服务。
***
### ACID
**原子性(Atomicity)：**是指一个事务要么全部执行，要么不执行，也就是说一个事务不可能只执行了一半就停止了。比如你从取款机取钱，这个事务可以分成两个步骤：1划卡，2出钱。不可能划了卡，而钱却没出来。这两步必须同时完成，要么就不完成。
**一致性(Consistency)：**是指事务的运行并不改变数据库中数据的一致性。例如，完整性约束了a+b=10，一个事务改变了a，那么b也应该随之改变。
**独立性(Isolation）：**事务的独立性也有称作隔离性，是指两个以上的事务不会出现交错执行的状态。因为这样可能会导致数据不一致。
**持久性(Durability）：**事务的持久性是指事务执行成功以后，该事务对数据库所作的更改便是持久的保存在数据库之中，不会无缘无故的回滚。
