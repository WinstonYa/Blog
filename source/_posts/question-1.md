---
title: 第1题：['1', '2', '3'].map(parseInt) what & why ?
date: 2020-05-23 11:59:28
categories: [前端面试题,javascript基础]
tags: 前端面试
---

- 先看一下**map**的语法：

  ```javascript
  var new_array = arr.map(function callback(currentValue[, index[, array]]) {
   // Return element for new_array 
  }[, thisArg])
  ```

  **callback**生成新数组元素的函数，使用三个参数：

  - **currentValue**：callback数组正在处理的当前元素。

  - **index** （可选）：callback数组中正在处理的当前元素的索引。

  - **array**（可选）：map方法调用的数组。

  - **thisArg**（可选）：执行callback函数时值被用作this。

  **返回值**：回调函数的结果组成了一个新的数组。

- ### parseInt

  parseInt()函数解析一个字符串参数，并返回一个指定基数的整数(数学系统的基础)。

  ```javascript
  parseInt(string, radix)
  ```

  - `string`要被解析的值。如果参数不是一个字符串，则将其转换为字符串（使用ToString抽象操作）。字符串开头的空白符将会被忽略。

  - `radix`表示要解析的数字的基数。该值介于 2 ~ 36 之间。

    如果省略该参数或其值为 0，则数字将以 10 为基础来解析。如果它以 “0x” 或 “0X” 开头，将以 16 为基数。

    如果该参数小于 2 或者大于 36，则 parseInt() 将返回 NaN。

    

  在`radix`为 undefined，或者`radix`为 0 或者没有指定的情况下，JavaScript 作如下处理：

  - 如果字符串 string 以"0x"或者"0X"开头, 则基数是16 (16进制).

  - 如果字符串 string 以"0"开头, 基数是8（八进制）或者10（十进制），那么具体是哪个基数由实现环境决定。ECMAScript 5 规定使用10，但是并不是所有的浏览器都遵循这个规定。因此，永远都要明确给出radix参数的值。

  - 如果字符串 string 以其它任何值开头，则基数是10 (十进制)。

    

  例子：parseInt("444",16) 表示将16进制的444转换成10进制的数，结果为1092。



## 本题解析：

```javascript
['1', '2', '3'].map(parseInt)
```

实际执行代码为：

```javascript
['1', '2', '3'].map((data,index) => {
	return parseInt(data,index)
})
```

返回值为：

```javascript
parseInt('1',0) // 1
parseInt('2',1) // NaN
parseInt('3',2) // NaN, 3>2,因此3不是2进制数
```

所以本题答案：

```javascript
['1', '2', '3'].map(parseInt);
// [1,NaN,NaN]
```



**扩展题**：

```JavaScript
['10','10','10','10','10'].map(parseInt);
// [10,NaN,2,3,4]
parseInt('10',0) // 10
parseInt('10',1) // NaN
parseInt('10',2) // 2
parseInt('10',3) // 3
parseInt('10',4) // 4
```

