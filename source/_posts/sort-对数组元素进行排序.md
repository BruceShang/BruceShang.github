---
title: sort()对数组元素进行排序
date: 2020-03-27 16:26:39
tags: ['sort()', '排序']
categories: ['js']
---
### 1.语法

```js
arr.sort([compareFunction])
```
##### 参数
> compareFunction 可选

1. 用来指定按某种顺序进行排列的函数。如果省略，元素按照转换为的字符串的各个字符的Unicode位点进行排序。

- <code>firstEl</code>
- 1. 第一个用于比较的元素。

- <code>secondEl</code>
- 2. 第二个用于比较的元素。

<!-- more -->

> 如果 compareFunction(a, b) 小于 0 ，那么 a 会被排列到 b 之前；
> 如果 compareFunction(a, b) 等于 0 ， a 和 b 的相对位置不变
> 如果 compareFunction(a, b) 大于 0 ， b 会被排列到 a 之前

### 2.比较数字类型的值，可以通过a-b来实现升序排列

```js
let numbers = [9, 8, 5, 2, 1, 0]
numbers.sort((a, b) => {
  return a - b;
})
console.log(numbers)
```

### 3.根据数组里的对象属性进行排序

```js
var items = [
  { name: 'Edward', value: 21, txtName: '震耳欲聋' },
  { name: 'Sharpe', value: 37, txtName: '锦上添花' },
  { name: 'And', value: 45, txtName: '八仙过海' },
  { name: 'The', value: -12, txtName: '我行我素' },
  { name: 'Magnetic', value: 33, txtName: '大吉大利' },
  { name: 'Zeros', value: 37, txtName: '五福临门' }
];

// 根据value值（数字类型）
items.sort(function (a, b) {
  return (a.value - b.value)
});

// 根据name字段 - 字母 
items.sort(function(a, b) {
  var nameA = a.name.toUpperCase();
  var nameB = b.name.toUpperCase();
  if (nameA < nameB) {
    return -1;
  }
  if (nameA > nameB) {
    return 1;
  }
  return 0;
});

// 根据数组对象里的 汉字 进行排序
/* 
* 当排序非 ASCII 字符的字符串（如包含类似 e, é, è, a, ä 等字符的字符串）一些
* 非英语语言的字符串需要使用 String.localeCompare。这个函数可以将函数排序到正
* 确的顺序。
*/
items.sort(function(a, b) {
  return a.txtName.localeCompare(b.txtName);
})
```
[localeCompare详解](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare)

### 4.使用映射改善排序

> compareFunction 可能需要对元素做多次映射以实现排序，尤其当 compareFunction 较为复杂，且元素较多的时候，某些 compareFunction 可能会导致很高的负载。使用 map 辅助排序将会是一个好主意。基本思想是首先将数组中的每个元素比较的实际值取出来，排序后再将数组恢复。

```js
// 需要被排序的数组
var list = ['Delta', 'alpha', 'CHARLIE', 'bravo'];

// 对需要排序的数字和位置的临时存储
var mapped = list.map(function(el, i) {
  return { index: i, value: el.toLowerCase() };
})

// 按照多个值排序数组
mapped.sort(function(a, b) {
  return +(a.value > b.value) || +(a.value === b.value) - 1;
});

// 根据索引得到排序的结果
var result = mapped.map(function(el){
  return list[el.index];
});
```