---
title: JavaScript深入浅出笔记（一）
date: 2018-07-27 16:52:00
categories: "JavaScript"
tags:
	- JavaScript
	- 视频笔记
---
## 大纲
[视频链接](https://www.imooc.com/learn/277)
- 数据类型
- 表达式和运算符
- 语句
- 对象
- 数组
- 函数
- this
- 闭包
- OOP
- 正则与模式匹配
<!--more-->
## 数据类型

弱类型的特性，隐式转化逻辑
```javascript
32+32 //64
"32"+32 //"3232" 字符串拼接
"32"-32 //0
```
六种数据类型
	原始类型（`number string boolean nulll undefined`）没有属性和方法
			+
	对象 object（`Date Array Function ...`）

严格等于（ `===`）
	1、类型不同，返回false
	2、类型相同 
等于（ `==` ）
	1、类型相同，同 `===`
	2、类型不同，尝试类型转化和比较
```javascript
	null ==undefined -> true
	number == string 转number -> 1== "1.0" true
	boolean == ? 转number -> 1==true  true
	object == number | string 尝试对象转基本类型 new String("hi") == 'hi' true 其他：false
```
----------
包装对象
```javascript
var a = "string";
a.length; //6 
//js会智能的把基本类型转为对应的包装对象 相当于new String 随后临时对象会被销毁
a.t =3;
a.t; //undefind
```
----------
类型检测
```javascript
typeof //最常见的 适合是不是函数对象和基本类型的判断
	例外：typeof null === "object" 
instanceof //适合对象类型判断 obj instanceof Object
	[1,2] instanceof Array === true
	new Obejct() instanceof Array === false
```
任何一个构造函数都会有一个`prototype`对象属性，这个对象属性将用作new来构造出对象的原型，会维持一个原型链（Obj._proto_<Student>._proto_<Person>...继承关系），**需注意是不同Window或iframe间的对象类型检测不能使用instanceof，检测都是false**

```javascript
Object.prototype.toString.apply([]); === "[object Array]"; 
Object.prototype.toString.apply(null); === "[object Null]"; 
IE6/7/8 Object.prototype.toString.apply(null); === "[object Object]"; 
```

其它 ` constructor 、 duck type`

## JavaScript表达式与运算符

> 表达式是一种JS短语，可使JS解释器用来产生一个值 

### 运算符
逗号运算符
它会从左到右依次去计算表达式的值最后取最右边的
```javascript
var val = (1,2,3); //val = 3
```
----------
delete 运算符 
删除对象上的属性
```javascript
var obj = {x:1};
obj.x; //1
delete obj.x;
obj.x; //undefined
//从IE9开始
var obj = {x:1};
//并不是对象上的所有属性都可以被delete掉的
Object.defineProperty(obj,'x',{
	configurable:false,
	value:1
})
delete obj.x; // false
obj.x; //1
```
----------
in 运算符
```javascript
obj.x = 1;
'x' in obj; //true
```
----------
new 运算符
```javascript
function Foo(){}
Foo.prototype.x = 1;
var obj = new Foo();
obj.x; //1
obj.hasOwnProperty('x'); //false 判断'x'属性是原型链上的还是对象本身上的
obj.__proto__.hasOwnProperty('x'); //true
```
----------
this 运算符
```javascript
this; // window(浏览器)
var obj = {
	func:function(){return this;}
}
obj.func; //obj
```
----------
void 一元运算符
` void 0 //undefined void(0) //undefined`

### 运算符优先级

| 运算符      |     描述 |   
| :-------- | --------:| 
| `. [] ()`    |   字段访问、数组下标、函数调用以及表达式分组 |  
| `++ -- - ~ ! delete new typeof void` | 一元运算符、返回数据类型、对象创建、未定义值 |  | 
| `* / %`    |  乘法、除法、取模 |  
| `<< >> >>>`    |  移位 |  
| `== != === !==`   |   等于、不等于、严格相等、非严格相等 |
| ` & `    |   按位与 |  
| ` ^ `   |   按位异或 |  
| ` 丨 `    |   按位或 | 
| ` && ` |  逻辑与 | 
| ` 丨丨 ` |  逻辑或 | 
| ` ?: ` |  条件运算符 | 
|  ` = oP= `  | 赋值 | 
|  ` , ` |  逗号 | 

## 语句

### block语句、var语句

block 语句
块语句常用于组合0 ~ 多个语句。块语句用一对花括号定义。
**没有块级的作用域，Java等很多语言都是有块级作用域的**
```javascript
for(var i =0;i<10;i++){}
i //10
```
但是JS有全局作用域、函数作用域等作用域

var 语句
` var a = b = 1 //这样写本身不会错，但是写在函数里面那么b将会是一个全局变量`

### try-catch

三种表示形式（和Java一样）
```javascript
try{}catch(e){}
try{}finally{}
try{}catch(e){}finally{}
```

### 函数、switch、循环

```javascript
function fd(){}	//函数声明 
var fe = function(){}; //函数表达式
//fd() 会被函数前置，就是在fd（）函数声明代码之前调用也是可以的，表达式就不行
```


----------


for...in
```javascript
for(var i in [1,2,3]){}
```
坑：
1. 顺序不确定（依赖引擎实现）
2. enumerable 为false时不会出现
3. for in 对象属性时受到原型链影响

with <已经不建议使用了> 
可以修改当前的作用域
```javascript
with({x:1}){
	console.log(x);
}
with(document.forms[0]){
	console.log(name.value);
}
	||
var form = document.forms[0];
console.log(form.name.value);
```

### 严格模式

> 严格模式是一种特殊的执行模式，它修复了部分语言上的不足，提供更强的错误检查，并增强安全性。是向上兼容的。

```javascript
function func(){
	'use strict'; //第一行
}
```
- 严格模式下不允许使用with
- 不允许为声明的变量被赋值
- arguments变为参数的静态副本（与形参没有相互的绑定关系了，但是参数是对象去修改参数属性的话依旧会影响` arguments[0].x = 100; `）
- delete参数报错
- 对象字面量重复属性名报错
- 禁止八进制字面量
- eval，arguments变为关键字，不能作为变量、函数名
- eval变为独立作用域

## 对象

对象中包含一系列属性，这些属性是**无序**的。每个属性都有一个**字符串key**和对应的value。每个对象还有一个原型，会有一个 [[ proto ]] 对象属性。还有[[class]]、[[extensible]]

对象中每一个属性都有很多标签（writeable、enumerable、configurable、value、get/set）

### 创建对象

```javascript
var o = new Object;
function foo(){} /*定义一个函数对象,这个函数对象会默认带一个prototype属性，它的属性是一个对象属性。(foo.prototype) 是一对象，每一个对象都有[[proto]]标签。这里的[[proto]] 指向-> Object.prototype -> null*/
foo.prototype.z = 3; //往(foo.prototype) 对象中添加了一个属性 "z=3"

var obj = new foo(); /*用new的方式构造了一个新对象，通过new这种方式构造对象的她点就是它的原型[[proto]]会指向构造器的prototype属性（foo.prototype），这么做的好处是可以访问原型链上的属性一直找到null*/
obj.y = 2;
obj.x = 1; //通过赋值操作，添加了两个属性给obj对象

obj.x; //1
obj.y; //2
obj.z; //3
typeof obj.toString; // 'function' toString 是 Object.prototype 中的
'z' in obj; //true
obj.hasOwnProperty('z'); //false

obj.z = 5; /*赋值时，先看obj中有没有这个值，如果有的话就修改，没有的话就直接添加，不会去修改或者删除 foo.prototype 中的值。*/
obj.z; // 5

delete obj.z; //true
obj.z; //3

delete obj.z; //true
obj.z; //3 , still 3!
```

----------

通过 Object.create 创建对象
```javascript
var obj = Object.create({x:!});
obj.x; //1
obj.hasOwnProperty('z'); //false
// 原型链 obj -> {x:1} -> Object.prototype -> null

var obj = Object.create(null);
obj.toString //undefined
// 原型链 obj -> null
```

### 属性操作

```javascript
var obj = {x:1,y:2};
obj.x; // 1 一般建议这种
obj['y']; //2

//in操作符 是会往原型链向上查找的
for(var i in obj){ //可能会把原型链上的属性遍历出来，顺序是不确定的
	console.log(obj[i]); 
}

var i = 1 && 2 && 3; //i=3 巧用运算符，只有前面的值都存在时才会把最后一个值赋予

var descriptor = Object.getOwnPropertyDescriptor(Obejct,'prototype');
// 可以去获取一个属性中的所有标签，这里则是获取的Object对象的prototype属性的标签
descriptor.configurable; //false 是否可配置 delete？
```

> 需注意的是我们用对象自变量，new Obejct 去创建的对象，或者是通过赋值去创建这样的属性默认既可以枚举也可以写也可以delete，都是true的，而通过defineProperty这种方式创建的话默认都是false。

```javascript
var cat = new Obejct;
cat.propertyIsEnumerable('toString'); //false 是否可枚举的
Obejct.defineProperty(cat,'price',{enumerable:false,value:1000});
cat.propertyIsEnumerable('price');//false

```

#### 属性的枚举
```javascript
var o = {x : 1,y : 2};
'toString' in o; //true
o.propertyIsEnumerable('toString'); //false 
var key;
for(key in o){
	console.log(key); // x,y,z
}

var obj = Object.create(o);
obj.a = 4;
var key;
for(key in o){
	console.log(key); // a,x,y,z
	if(obj.hasOwnProperty(key)){
		console.log(key); // a
	}
}
```

### get/set方法

```javascript
var man = {
	name : 'A',
	$age: null,
	get age(){
		return this.$age;
	},
	set age(val){
		val = +val; //转换为数字
		if(!isNaN(val)){
			this.$age = val;
		}else{
			throw new Error('Incorrent value'+val);
		}
	}
}

man.age; // 自动调用get方法
man.age = 100; //自动调用set方法
```

### 属性标签

```javascript
Object.getOwnPropertyDescriptor({pro:true},'pro');
// (对象，'属性名')查看属性标签
/*
{…}
	​configurable: true //是否可以再被修改，被 delete
	​enumerable: true //是否可被枚举
	​value: true
	​writable: true //是否可写，修改
	​<prototype>: {…}
		​​__defineGetter__: function __defineGetter__()
		​​__defineSetter__: function __defineSetter__()
		​​__lookupGetter__: function __lookupGetter__()
		​​__lookupSetter__: function __lookupSetter__()
		​​constructor: function Object()
		​​hasOwnProperty: function hasOwnProperty()
		​​isPrototypeOf: function isPrototypeOf()
		​​propertyIsEnumerable: function propertyIsEnumerable()
		​​toLocaleString: function toLocaleString()
		​​toSource: function toSource()
		​​toString: function toString()
		​​valueOf: function valueOf()
*/
//去创建管理这些属性
var person = {};
Object.defineProperty(person,'name',{ 
	configurable : true,
	​enumerable : false,
	​value : "Alen"
	​writable : true
});

Object.keys(person); // []  获取对象上的所有的key 

Object.defineProperties(person,{
	title2:{enumerable:true,value:"da"},
	salary:{enumerable:true,value:50000,writable:true},
	promote:{set:function(level){
		this.salary *= 1 + level*0.1;
	}}
});
Object.keys(person); // Array [ "title2", "salary" ]
erson.salary; //50000
person.promote = 2; //2
person.salary; //60000

```

|   /  | ` configurable:true, writable:true `|   ` configurable:true, writable:false `   |   ` configurable:false, writable:true `   |   ` configurable:false, writable:false `   |
| :-------- | --------:| :------: |:------: |:------: |
| 修改属性的值|  ✔  |  ✔ 重设value标签修改 |✔ |  **X**  |
| 通过属性赋值修改属性值|   ✔ |  **X**  |✔ |  **X**  |
| delete 返回 true|   ✔ |  ✔  |**X**|  **X**  |
| 修改getter/setter方法|   ✔ |  ✔  |**X**|  **X**  |
| 修改属性标签 （writable从true改为false总是运许）|   ✔ |  ✔  |**X** |  **X**  |

### 对象标签、对象序列化

`[[proto]]`

`[[class]]` 表示对象是哪一个类型，` Object.prototype.toString `会把参数变为对象再做处理
```javascript
var toString = Object.prototype.toString;
function getType(o){return toString.call(o).slice(8,-1);}
toString.call(null); //"[object Null]"
getType(null); //"Null"
```

`[[extensible]]` 表示这个对像是否可扩展
```javascript
var obj = {x:1,y:2};
Object.isExtensible(obj); // true 默认
Object.preventExtensible(obj); 
Object.isExtensible(obj); //false
obj.z = 1;
obj.z;//undefined
Object.seal(obj); // configurable ->false
Object.isSealed(obj); //true
Object.freeze(obj); // writable -> false configurable ->false
Object.isFrozen(obj); //true 
```

#### 序列化

```javascript
var obj = {x:1,y:2,val:undefined,a:NaN,c:new Date()};
JSON.stringify(obj); 
// "{\"x\":1,\"y\":2,\"a\":null,\"c\":\"2018-07-27T06:37:28.989Z\"}"
//JSON.stringify 如果属性中有undefined，那么这个值将不会被转化，NaN ->null 

obj = JSON.parse('{"x":1}'); //注意合法的JSON 属性名必须用双引号
obj.x; //1
```

序列化-自定义
```javascript
var obj = {x:1,o:{o1:1,o2:2,toJSON:function(){return this.o1+this.o2}}};
JSON.stringify(obj);// "{\"x\":1,\"o\":3}"
```

####  其它对象方法
```javascript
var obj = {x:1,y:2};
obj.toString(); // "[object Object]"
"Result"+obj; // "Result[object Object]"
obj.toString = function(){return this.x+this.y}; //重写toString方法
"Result"+obj; // "Result3"
+obj; // 3

obj.valueOf = function(){return this.x+this.y+100}; 
//尝试把对象转为基本类型时候调用的函数
+obj; // 103
"Result"+obj; // "Result103"
```

> 当valueOf  和 toString都存在时 拼接和 + 时，都会尝试把对象转化为基本类型，会先去valueOf 若返回的是对象则会去找toString