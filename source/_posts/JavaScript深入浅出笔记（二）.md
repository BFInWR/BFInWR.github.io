---
title: JavaScript深入浅出笔记（二）
date: 2018-07-31 16:52:00
categories: "JavaScript"
tags:
	- JavaScript
	- 视频笔记
description: "记录JavaScript深入浅出视频的笔记"
---
# JavaScript深入浅出笔记（二）

## 数组

**JS中的数组是弱类型的，数组中可以含有不同的类型元素**

1. 字面量数组创建 ` var BAT = ['B','A','T']; `
	数组允许最后一个值后留一个逗号
2. new Array 创建数组
	` var arrLength = new Array(100); //undefined * 100` 
	` var BAT= new Array('B','A','T'); `

### 数组的读写
```javascript
var arr = [1,2,3,4,5];
arr[1]; //2
arr.lengthl; //5
arr[5] = 6;
arr.length; //6

delete arr[0];
arr[0]; //undefined
arr.length; //6
1 in arr; //false delete删除的没有这个key

arr[arr.length] = 7; // equal to arr.push(7);在尾巴添加

arr.unshift(0); //在头添加

arr.pop(); //equal to arr.length -= 1; 在尾部删除元素
arr.shift(); //在头部删除一个元素
```

**数组是动态的不用指定大小**

### 二维数组 & 稀疏数组
``` javascript
var arr = [[0,1],[2,3],[4,5]];
```
稀疏数组并不含有从0开始的**连续**索引，一般length属性值比实际元素个数大。
```javascript
var arr1 = [undefined];
0 in arr1; //true
var arr = [,,];
0 in arr; //false
```

### 数组方法

```
{} => Object.prototype
[] => Array.prototype
```
```javascript
Array.prototype.join
Array.prototype.reverse
Array.prototype.sort
Array.prototype.concat
Array.prototype.slice //切片
Array.prototype.splice //交接
Array.prototype.forEach(ES5)
Array.prototype.map(ES5)
Array.prototype.filter(ES5)
Array.prototype.every(ES5)
Array.prototype.some(ES5)
Array.prototype.reduce/reduceRight(ES5)
Array.prototype.indexOf/lastindexOf(ES5)
Array.isArray(ES5)
```

----------

**join 数组转字符串**

```javascript
var arr = [1,2,3];
arr.join(); //"1,2,3"
arr.join("_"); // "1_2_3"
new Array(4).join("a"); //"aaa"
```

----------


**reverse 数组逆序**

```javascript
var arr = [1,2,3;
arr.reverse(); //[3,2,1]
arr; // [3,2,1]
```

----------

**sort 排序（默认是按字母排序）**

```javascript
var arr = ["a","d","b","c"];
arr.sort(); //[ "a", "b", "c", "d" ] arr; 原数组被修改

arr = [13,24,51,3];
arr.sort();//[ 13, 24, 3, 51 ]
arr.sort(function(a,b){
	return a - b; //升序
}); //[ 3, 13, 24, 51 ]

arr = [{age:25},{age：39}，{age:99}];
arr.sort(function(a,b){
	return a.age - b.age; 
});
arr.forEach(function(item){
	console.log('age',item.age);
})
```

----------

**concat 数组合并**
```javascript
var arr = [1,2,3];
arr.concat(4,5); //[1,2,3,4,5]
arr; //[1,2,3] 原数组未被修改
arr.concat([10,11],13); //[1,2,3,10,11,13]
arr.concat([1,[2,3]]); //[1,2,3,1,[2,3]];
```

----------

**slice 返回部分数组**
```javascript
var arr = [1,2,3,4,5];
arr.slice(1,3);//[2,3]
arr.silce(1,-1);//[2,3,4]
```

----------

**splice 数组拼接**
```javascript
var arr  = [1,2,3,4];
arr.splice(2); // returns [3,4]
arr; //[1,2] 原数组修改

arr  = [1,2,3,4,5];
arr.splice(2,3); //returns [3,4]
arr;//[1,2,5]

arr  = [1,2,3,4,5];
arr.splice(1,1,'a','b'); //returns[2]
arr; //[1,"a","b",3,4,5]
```

----------

**forEach 数组遍历**
```javascript
var arr = [1,2,3];
arr.forEach(function(x,index,a){ //值,索引,指向数组本身
	console.log(x+'|'+index+'|'+a);
})
/*
1|0|1,2,3
2|1|1,2,3
3|2|1,2,3
*/
```

----------

**map 数组映射**
```javascript
var arr = [1,2,3];
arr.map(function(x){
	return x+10;
});//[11,12,13]
arr; //[1,2,3] 不会修改原数组
```

----------

**filter 数组过滤**
```javascript
var arr = [1,2,3,4];
arr.filter(function(x,index){
	return x%2 === 0 && index<3;
}); // [2]
arr; // [1,2,3,4] 不会修改原数组
```

----------

**every & some 数组判断**
```javascript
var arr = [1,2,3,4];
arr.every(function(x){
	return x<10;
});//true 必须每一个值都满足才返回true 而some只需要一个元素满足即可
```

----------

**reduce & reduceRight  数组聚合成结果**
数组两两之间做一个操作
```javascript
var arr = [1,2,3];
var sum = arr.reduce(function(x,y){
	return x+y;
},0); //6 第二个元素 0 是一个可选参数 当填进去的时候 函数第一次第一个参数 x = 0 // y=1 ， 第二次 x 是第一次的返回值 y =2
arr;//[1,2,3]

arr = [3,9,6];
var max = arr.reduce(function(x,y){
	console.log(x+"|"+y);
	return x>y?x:y;
});//3|9 9|6
max;//9
```
reduceRight 是从数组的尾巴开始遍历

----------

**indexOf & lastIndexOf 数组检索**
```javascript
var arr= [1,2,3,2,1];
arr.indexOf(2);//1 查找元素2的下标
arr.lastIndexOf(2);//3
arr.indexOf(22); //-1 没有这个元素
arr.indexOf(1,1);//4 从下标为1的元素开始查找1 
arr.length(1,-3); //4
arr.indexOf(2,-1); //-1 
```
lastIndexOf 是从数组的尾巴开始查找

----------

**Array.isArray 判断是否为数组**
```javascript
Array.isArray([]); // true

[] instanceof Array;// true
({}).toString.apply([]) === '[object Array]'; //true
[].constructor === Array; //true
```

## 数组小结

**数组 VS 一般对象**

- 相同
	都可以继承
	数组是对象，对象不一定是数组
	都可以当做对象添加删除属性
- 不同
	数组自动更新length
	按索引访问数组常常比访问一般对象属性明显迅速
	数组对象继承Array.prototype上的大量数组操作方法

**字符串和数组**

- 字符串是类数组
```javascript
var str = "hello";
str.charAt(0); //"h"
str[1]; //e

Array.prototype.join.call(str,"_");
//"h_e_l_l_o" call将对象装化为数组
```

## 函数和作用域

### 函数概述

> 函数是一块JavaScript代码，被定义一次，但可执行和调用多次。
> JS中的函数也是对象，所以JS函数可以像其它对象那样操作和传递
> 所以我们也常叫JS中的函数为函数对象。
> 一般函数调用若是没有return 语句这会返回undefined
> 若是作为构造器外部使用new调用，若是没有return 或者是return 基本类型则会返回this

### 声明与表达式

> 创建函数有不同的方式，常见的两种就是函数声明` function add(){} `函数表达式
```javascript
var add = function(){}; 
(function(){})(); //立即执行函数表达式 函数声明不能立即调用
return function(){}; //函数表达式作为返回值
var add = function foo(){}; //命名式函数表达式
```
**区别**
- 函数声明会被前置，函数表达式只会变量声明提前

**命名函数表达式**
```javascript
var func = function nfe(){}; 
//可以在调试的时候显示函数名 否则会显示[anomyous function]
func === nfe;//IE6~8 false ,IE9+ 'nfe' is undefined
//递归调用
var func = function nfe(){nfe();}
```

**Function构造器**
```javascript
// 没有函数字 只能匿名 不会前置
//表示a和b有两个形参 
var func = new Function('a','b','console.log(a+b);');
func(1,2); //3
//这两种没有区别
var func = Function('a','b','console.log(a+b);');
func(1,2); //3

Function('var localVal = "local";console.log(localVal);')();//立即执行
console.log(typeof localVal); //拿不到Function中定义的localVal
//local undefined 

var globalVal = 'global';
(function(){
	var localVal = 'local';
	Function('console.log(typeof localVal,typeof globalVal);')()
})();
// undefined  string 
// 可以拿到全局变量globalVal 但是拿不到外层的局部变量localVal 
```

### this

**全局的this(浏览器)**
```javascript
this.document === docuemnt //true
this === window //true
this.a = 37; //全局变量a
window.a //37
```

**一般函数this**
```javascript
function f1(){
	return this;
}
f1() === window;//ture | global object

function f2(){
"use strict";
return this;
}
f2() === undefined; //true
```

**作为对象方法的函数this 是最常见的用法**
```javascript
var o = {prop :37};
function func(){return this.prop;}
o.f = func;
o.f(); //37

//原型链上的this
var o = {f:function(){return this.a+this.b;}};
var p = Object.create(o);
p.a = 1;
p.b = 4;
p.f(); //5

//call/apply方法
add.call(o,1,2); //第一个参数作为this传给add方法

//bind方法 ES5提供的方法 IE9+
function f(){return this.a;}
var g = f.bind({a:"test"}); //把对象绑定作为this传入f
g(); //test
var o = {a:37,f:f,g:g};
o.g +"" o.g //37 "test"
```

### 函数属性&arguments
```javascript
function foo(x,y,z){
	arguments.length; //2 实参个数
	arguments[0] = 10; //非严格下形参x可以改变
	arguments[2] = 10; //没有参数传进来 则无论如何都会影响
	arguments.callee === foo; //true 非严格下才能用
}
foo(1,2);
foo.length; //3 形参个数
foo.name; //foo
```

**bind与currying 改变this的指向 函数柯里化**
```javascript
this.x = 10;
var m = {x:100,getX:function(){return this.x;}};
m.getX(); //100
var getX = m.getX();
getX(); //10
var bindGetX = getX.bind(m);
bindGetX(); //100

function add(a,b,c){return a+b+c}
var func = add.bind(undefined,100); //第一次绑定 固定a=100
func(1,2); //103

var func2 = func.bind(undefined,200); //第二次绑定 固定b = 200
func2(10); //310
```
