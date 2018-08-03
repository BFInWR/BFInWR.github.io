---
title: JavaScript深入浅出笔记（END）
date: 2018-08-03 14:52:00
categories: "JavaScript"
tags:
	- JavaScript
	- 视频笔记
description: "记录JavaScript深入浅出视频的笔记"
---
# JavaScript深入浅出笔记（END）

## OOP 

**因为JavaScript是弱类型，没有直接的机制去实现参数的重载，在JS中函数传参类型不确定，个数也是任意的，可以通过判断实际传入参数的个数，去模拟重载**

```javascript
function Person(){
	var args = arguments;
	if(typeof args[0] === 'object' && args[0]){//因为null也是object
		if(args[0].name){this.name = args[0].name;}
		if(args[0].age){this.age = args[0].age;}
	}else{
		if(args[0]){this.name = args[0];}
		if(args[1]){this.age = args[1];}
	}
}
Person.prototype.toString = function(){ //重写toString方法 可以调用
	return this.name+""+this.age;
} 

var bosn1 = new Person('Bson',27);//传入两个参数
var bson2 = new Person({name:‘N',age:12}); //传入一个对象
```

**调用基类**
```javascript
function Person(name){this.name = name;}
function Student(name,className){
	this.className = className;
	Person.call(this,name); //调用基类的构造器
}
var bosn = new Student('Bson','ABC');
bson; //Student{className:"ABC",name:"Bosn"}

Person.prototype.init = function(){};
Student.prototype.init = function(){
	//do sth...
	Person.prototype.init.apply(this.arguments); //调用基类的方法
};
```

**模块化**
```javascript
var moduleA;
moduleA = function(){
	var prop = 1;
	function func(){
		return {
			func:func,
			prop:prop
		}
	}
}();

var moduleA;
moduleA = new function(){
	var prop = 1;
	function func(){}
	this.func = func;
	this.prop = prop;
};
```

## 实践-探测器

**立即执行的匿名函数，防止变量、函数声明这些泄露到外部**

```javascript
!function(global){ // 匿名函数 最外层只传了this 一般模式下 会指向全局对象
	function DetectorBase(configs){ //探测器基类
		if(!this instanceof DetectorBase){//为了不让外部通过直接调用的方式调用
			throw new Error('Do not invoke without new');
		}
		this.configs = configs;//配置
		this.analyze(); //解析数据
	}
	
	DetectorBase.prototype.detect = function(){ //模板方法 探测
		throw new Error("Not implemented");// 必须实现这个方法否则报错 抽象
	};
	DetectorBase.prototype.analyze = function(){
		console.log('analyzing.....');
		this.data = "DATA....";
	};

	function LinkDetector(links){ //链接探测器
		if(!this instanceof LinkDetector){//同样必须使用new去构造
			throw new Error('Do not invoke with new.');
		}
		this.links = links;
		DetectorBase.apply(this,arguments); //调用基类的构造器
	}
	
	inherit(LinkDetector,DetectorBase); //实际原型链的继承
	//LinkDetector.prototype.xxx = xxx ; 再扩展,继承时会改写prototype属性
	function inherit(subClass,superClass){
		subClass.prototype = Object.create(superClass.prototype);
		subClass.prototype.constructor = subClass;
	}
	
	LinkDetector.prototype.detect = function(){ //子类重写detect方法
		console.log("Loading data: "+this.data);
		console.log("Scaning links: "+this.links)
	}
	
	
	//防止被修改
	Object.freeze(DetectorBase);
	Object.freeze(DetectorBase.prototype);
	Object.freeze(LinkDetector);
	Object.freeze(LinkDetector.prototype);
	
	//导出到全局对象 global就是全局对象 definProperties属性都是false
	Object.defineProperties(global,{
		LinkDetector:{value:LinkDetector},
		DetectorBase:{value:DetectorBase}
	});
}(this);

var ld = new LinkDetector('http://www.baidu.com http://www.qq.com');
//analyzing.....
ld.detect();
//Loading data: DATA....
//Scaning links: http://www.baidu.com http://www.qq.com
```

## 正则表达式

```javascript
/\d\d\d/.test("123"); //true

new RegExp("Bson").test("Hi,Bson"); //true 
RegExp("Bson").test("Hi,Bson"); //true 
```

| 格式		|     意义  |   Demo(return true)   |
| :-------- | --------:| :------: |
| `.` |   任意字符（除换行符）|  /.../.test('1a@');|
| `\d`|   数组0-9	|  /\d\d\d/.test("123");	|
| `\D`|   非`\d`:不是数字0-9的字符|  /\D\D\D/.test("ab!");	|
| `\w`|   0-9,A-Z,a-z,_|  /\w\w\w/.test("aB9_");|
| `\W`|   非`\w`	|  /\W\W\W/.test("@!$");	|
| `\s`|   空格符、TAB、换页符、换行符|  /\sabc/.test(" abc");|
| `\S`|   非`\s`	|  |
| `\t \r \n \v \f`	|   tab 回车 换行 垂直制表符 换页符 |  	|

| 范围符号 |     意义  |   Demo |
| :-------- | --------:| :------: |
| `[...]`|   字符范围 |  `[a-z] [0-9] [A-Z0-9a-z_]`|
| `[^...]`|   字符范围以外 |  `[^a-z] [^abc]`|
| `^`|  行首 |  `^Hi`|
| `$`|   行尾 |  `test$`|
| `\b`|   零宽单词边界 |  `\bno`|
| `\B`|   非`\b` | |

** 右斜线 \ 转义**

**分组**

| 格式	|     意义  |   Demo(return true)|
| :-------- | --------:| :------: |
| `(x)`	| 分组，并记录匹配到的字符串|  `/(abc)/`|
| `\n`	| 表示使用分组符`(x)`匹配匹配到的字符串|  `/(abc)\1/.test('abcabc')`|
| `(?:x)`| 仅分组不引用 |  `/(?:abc)(def)\1/.test('abcdefdef')`|

**重复**

| 格式	|     意义  |   Demo|
| :-------- | --------:| :------: |
| `x* x+`|`*`>=0 `+`>0 贪婪算法|abc* —> ab、abc、abcc abc+ —> abc、abcc|
| `x*? x+?`|同`x* x+` 非贪婪算法(少)|abcccc -> abc*? 匹配ab,abc+? 匹配abc|
| `x?`|   出现0或者1次|  |
| `x|y`|   x或者y|  |
| `x{n} x{n,} x{n,m}`|   重复n次 重复>=n次，重复次数满足n<=x<=m|  |


**三个Flag**
1. **global** 匹配所有的情况 —— g
2. **ignoreCase** 不区分大小写 —— i
3. **multiline** 有分行跨行时 —— m
```javascript
/abc/gim.test("ABC"); //true
RegExp("abc","gim");
```

**对象属性**
```javascript
/abc/g.global //true
/abc/g.ignoreCase //false
/abc/g.multiline //false
/abc/g.source //"abc" 正则的内容
```

**对象方法**
```javascript
/abc/.exec("abcdef"); //"abc" 匹配
/abc/.test("abcde"); //true
/abc/.toString(); // "/abc/"

var reg = /abc/;
reg.compile("def"); //改变正则的属性
ref.test("def"); //true
```

**string类型与正则相关的方法**
```javascript
String.prototype.search		"abcabcdef".search(/(abc)\1/); //0
String.prototype.replace	"aabbbbcc".replace(/b+?/,"1"); //aa1bbbcc
String.prototype.match	"aabbbbccbb".match(/b+/g);//["bbbb","bb"] g匹配完
String.prototype。split	   "aabbbbccbbaa".split(/b+/);//["aa","cc","aa"]
```