---
title: SQL优化
date: 2020-3-8 15:52:00
categories: "数据库"
tags:
	- SQL
	- MySQL
	
---
## SQL优化
所谓SQL调优，本质上就是：大量读用索引，索引不失效，慢查询explain调
<!--more-->
***
### SQL优化的一些方法
1. 索引
避免全表扫描，考虑在where和order by涉及的列上建立索引

2. where子句判断
避免在where中对字段进行null值判断，否则将导致引擎放弃使用索引进行全表扫描
例:
`select id from t where num is null`
避免在where中使用`!=`或`<>`操作符，否则将导致全表扫描
避免在where中使用`or`类连接条件，否则将导致全表扫描
例(用or多列都需要使用索引才能用)
```sql
select id from t where num=10 or num=20    
可以这样查询：    
select id from t where num=10    
union all    
select id from t where num=20    
```
避免在 where 子句中对字段进行表达式操作，否则将导致全表扫描
避免在where 子句中的“=”左边进行函数、算术运算或其他表达式运算
```sql
如：    
select id from t where num/2=100    
应改为:    
select id from t where num=100*2    
```
避免在where子句中对字段进行函数操作，否则将导致全表扫描
```
select id from t where substring(name,1,3)='abc'
--name以abc开头的id    应改为:    
select id from t where name like 'abc%'    
```


3. in&not in(也用 exists 代替 in )
in 和 not in 也要慎用，否则会导致全表扫描，如:
```sql
select id from t where num in(1,2,3)  
对于连续的数值，能用 between 就不要用 in 了： 
select id from t where num between 1 and 3
```
```sql
select num from a where num in(select num from b)    
用下面的语句替换：    
select num from a where exists(select 1 from b where num=a.num) 
```
4. like(%开头导致索引失效 全表扫描)
`select id from t where name like '%abc%'`

5. 复合索引作为条件
必须使用到该索引中的第一个字段作为条件时才能保证系统使用该索引，否则该索引将不会被使用，并且应尽可能的让字段顺序与索引顺序相一致。    

6. 生成空表结构
`create table #t(...)`

7. 索引并不是越多越好
因为 insert 或 update 时有可能会重建索引，所以怎样建索引需要慎重考虑，视具体情况而定。    
一个表的索引数最好不要超过6个，若太多则应考虑一些不常使用到的列上建的索引是否有必要。

8. 尽量使用数字型字段（数字型代替字符型）
因为引擎在处理查询和连接时会逐个比较字符串中每一个字符，而对于数字型而言只需要比较一次就够了。

9. varchar 代替 char
变长字段存储空间小，可以节省存储空间

10. 任何地方都不要使用 select * from t 

11. 尽量避免大事务操作，提高系统并发能力。