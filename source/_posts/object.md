---
title: 理解Object
date: 2018-02-01 20:35:57
tags: [Object]
categories: Object
---

## 1.创建对象的几种方法
1.1创建对象的最简单的方法就是使用对象直接量
```js
var emptyObj = {};
```
1.2 通过new创建对象
```js
var emptyObj = new Object();
```
<!--more-->

1.3 Object.create(prototype, [propertyObject])
使用Object.create()创建一个新对象，其中第一个参数是对象的原型，第二个是可选参数，用于对对象的属性进行进一步的描述。
propertyObject 为null时新创建的对象就不包括所有的属性和方法 var obj = Object.create(null) obj.toString => undefined
##### Object.create应用场景(实现继承)
```js
function Person() {}
function Student() {}
 // 这样做的好处是修改子类的属性不会对父类造成影响，可以在Student实现自己的属性和方法，但有一点Object.create是ES5的时候才有的方法，不兼容低版本，如果想要使用Object.create可以事先判断一下
Student.prototype = Object.create(Person.prototype)
if (!Object.create) {
  Object.create = function (proto) {
    function N () {}
    N.prototype = proto;
    return N;
  }
}
// 对上面的继承进行扩展，有一种更加便利的方式;
// 将Student原型对象中的构造函数指向Person
Student.prototype.constructor = Person
```
## 对象中的getter setter方法
```js
var objs = {
  name: 'Bruce',
  get deelName() {
    return this.name
  },
  set deelName(val) {
    this.name = val
  }
}
// 获取属性值 objs.deelName => Bruce
// 为对象属性赋值 objs.deelName = 'test'
// 验证赋值=> 'test'
// objs.name => 'test'

// 对象已有的属性添加特性描述
var book = {
  _year: 2004,
}
Object.defineProperty(book, 'year', {
  configurable: true, // 默认为true 是否可以配置（删除、修改、访问）
  enumerable: true, // 默认true 是否能枚举for-in
  writable: true, // 默认true 是否能修改属性的值
  get: function() {
    return this._year
  },
  set: function(newval) {
    this._year = newval
  }
})
// book.year => 2004
// book.year = 2005
// book.year => 2005
```