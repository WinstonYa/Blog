---
title: 判断js类型的方式
date: 2020-05-26 19:30:44
categories: [前端面试题,Javascript原理]
tags: 前端面试
---

### 第一种方式：typeof

**【语法】**

`typeof` 运算符后接操作数：

```javascript
typeof operand
typeof(operand)
```

**【typeof的返回值】**

​	typeof运算符的返回类型为字符串，值包括以下几种：

​		1.`String`				---> "string"

​		2.`Number`				---> "number"

​		3.`BigInt`				---> "bigint"

​		4.`Boolean`  			--->  "boolean"

​		5.`Undefine`			--->"undefined"

​		6.`Object` 				--->  "object"

​		7.`null`					---> "object" // JavaScript 诞生以来便如此

​		8.`function`			--->  "function"

​		9.`Symbol`[ES6新增]				---> "symbol"

​		10.宿主对象（由JS环境提供） --->  * 取决于具体实现*

**【typeof为什么要区分object和function？】**

​		答案一：《JavaScript高级程序设计》：从技术角度讲，函数在ECMAScript中是对象，不是一种数据类型。然而，函数也确实有一些		特殊的属性，因此通过typeof操作符来区分函数和其他对象是有必要的。

​		答案二：在实际的使用过程中有必要区分Object和Function，所以在typeof这里实现了。

**【typeof的不足之处】**

​	1.不能区分对象、数组、正则，对它们操作都返回"object"。

​	2.Safar5,Chrome7之前的版本对正则对象返回 "function"。

​	3.在 IE 6, 7 和 8 上，很多宿主对象是对象而不是函数。例如：

```javascript
typeof alert === 'object'
```

​	4.而在非IE浏览器或则IE9以上（包含IE9）。

```javascript
typeof alert === 'function'
```

**【特殊情况】**

1. Symbol（ES6新增）=> "symbol"。

2. 不能区分对象、数组、正则，返回"object"，正则在Safar5,Chrome7之前的版本中返回"function"。

3. 宿主对象，IE6/7/8返回"object"，其他浏览器返回"function"。

4. 特殊中的特殊:

   ```javascript
   typeof 1/0; //NaN（这个NaN不是字符串类型，是数值类型）
   typeof typeof 1/0; //NaN（这个NaN不是字符串类型，是数值类型）
   typeof(1/0); //"number"
   typeof typeof(1/0); //"string"
   typeof(typeof 1/0); //"number"
   ```

**【示例】**

```javascript
// 数值
typeof 37 === 'number';
typeof 3.14 === 'number';
typeof(42) === 'number';
typeof Math.LN2 === 'number';
typeof Infinity === 'number';
typeof NaN === 'number'; // 尽管它是 "Not-A-Number" (非数值) 的缩写
typeof Number(1) === 'number'; // Number 会尝试把参数解析成数值

typeof 42n === 'bigint';


// 字符串
typeof '' === 'string';
typeof 'bla' === 'string';
typeof `template literal` === 'string';
typeof '1' === 'string'; // 注意内容为数字的字符串仍是字符串
typeof (typeof 1) === 'string'; // typeof 总是返回一个字符串
typeof String(1) === 'string'; // String 将任意值转换为字符串，比 toString 更安全


// 布尔值
typeof true === 'boolean';
typeof false === 'boolean';
typeof Boolean(1) === 'boolean'; // Boolean() 会基于参数是真值还是虚值进行转换
typeof !!(1) === 'boolean'; // 两次调用 ! (逻辑非) 操作符相当于 Boolean()


// Symbols
typeof Symbol() === 'symbol';
typeof Symbol('foo') === 'symbol';
typeof Symbol.iterator === 'symbol';


// Undefined
typeof undefined === 'undefined';
typeof declaredButUndefinedVariable === 'undefined';
typeof undeclaredVariable === 'undefined'; 


// 对象
typeof {a: 1} === 'object';

// 使用 Array.isArray 或者 Object.prototype.toString.call
// 区分数组和普通对象
typeof [1, 2, 4] === 'object';

typeof new Date() === 'object';
typeof /regex/ === 'object'; // 历史结果请参阅正则表达式部分


// 下面的例子令人迷惑，非常危险，没有用处。避免使用它们。
typeof new Boolean(true) === 'object';
typeof new Number(1) === 'object';
typeof new String('abc') === 'object';

// 函数
typeof function() {} === 'function';
typeof class C {} === 'function'
typeof Math.sin === 'function';
```

[具体方法请参考](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof)



### 第二种方式：instanceof

**【原理】**

**`instanceof`** **运算符**用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链上。

**【语法】**

```javascript
object instanceof constructor
```

**参数：**

`object`：某个实例对象

`constructor`：构造函数

**【示例】**

```javascript
// 定义构造函数
function C(){} 
function D(){} 

var o = new C();


o instanceof C; // true，因为 Object.getPrototypeOf(o) === C.prototype


o instanceof D; // false，因为 D.prototype 不在 o 的原型链上

o instanceof Object; // true，因为 Object.prototype.isPrototypeOf(o) 返回 true
C.prototype instanceof Object // true，同上

C.prototype = {};
var o2 = new C();

o2 instanceof C; // true

o instanceof C; // false，C.prototype 指向了一个空对象,这个空对象不在 o 的原型链上.

D.prototype = new C(); // 继承
var o3 = new D();
o3 instanceof D; // true
o3 instanceof C; // true 因为 C.prototype 现在在 o3 的原型链上
```

[具体方法请参考](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof)



### 第三种方式： Object.prototype.toString.call()

**【语法】**

```javascript
Object.prototype.toString.call()
```

**【示例】**

可以通过 `toString()` 来获取每个对象的类型。为了每个对象都能通过 `Object.prototype.toString()` 来检测，需要以 `Function.prototype.call()` 或者 `Function.prototype.apply()` 的形式来调用，传递要检查的对象作为第一个参数，称为 `thisArg`。

```javascript
var toString = Object.prototype.toString;

toString.call(new Date); // [object Date]
toString.call(new String); // [object String]
toString.call(Math); // [object Math]

//Since JavaScript 1.8.5
toString.call(undefined); // [object Undefined]
toString.call(null); // [object Null]
```

[具体方法请参考](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)



### 第四种方式： Array.isArray()

**【语法】**

```js
Array.isArray(obj) //Array.isArray() 用于判断传递的值是否是一个数组。
```

**【示例】**

```js
// 下面的函数调用都返回 true
Array.isArray([]);
Array.isArray([1]);
Array.isArray(new Array());
Array.isArray(new Array('a', 'b', 'c', 'd'))
// 鲜为人知的事实：其实 Array.prototype 也是一个数组。
Array.isArray(Array.prototype); 

// 下面的函数调用都返回 false
Array.isArray();
Array.isArray({});
Array.isArray(null);
Array.isArray(undefined);
Array.isArray(17);
Array.isArray('Array');
Array.isArray(true);
Array.isArray(false);
Array.isArray(new Uint8Array(32))
Array.isArray({ __proto__: Array.prototype });
```

[具体方法请参考](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray)