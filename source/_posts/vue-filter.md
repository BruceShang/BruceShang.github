---
title: vue filter
tags: [Vue, Vue-filter]
date: 2016-10-10 15:30:50
categories: Vue
---

## Vue过滤器的基本使用

#### html部分
```html
<div id="app">
    <!--插值中的表达式-->
    <input type="text" v-model="msg">
    <h2>{{ msg ? 'YES' : 'NO' }}</h2>
    <h2>{{ msg.split('').reverse().join('') | uppercase }}</h2>
    <h2>{{ number + 1 | currency '₫' 0 }}</h2>
    <!--自定义过滤器-->
    <h2> {{ filterParam | customFilter}}</h2>
    <!--
    1.limitBy - test
    2.限制只显示数字中前两条数据
     -->
    <ul>
        <li v-for="item in dataArray | limitBy 2">name: {{ item.name }}----age: {{ item.age }}</li>
    </ul>
    <!--
     1.filterBy - test
     2.过滤出对象中name属性字段含有‘Chuck’值的数据，并且只显示该数据
     3.in 后面可以跟多字段进行搜索
     -->
    <ul>
        <li v-for="item in dataArray | filterBy 'Chuck123' in 'name' 'temp'">name: {{ item.name }}----age: {{ item.age }}</li>
    </ul>
</div>
```
<!--more-->
#### js部分
```js
/**
   * 1.split 将字符串分割成数组
   * 2.filter 接受两个参数，第一个是自定义名称，第二个是一个函数，并将值作为参数使用最后返回处理后的值
   * 3.reverse 用于颠倒数组之前的顺序
   * join 将数组按照指定分隔符进行分割，并返回一个字符串
   * */
  Vue.filter('customFilter', function(value) {
      return value.split('').reverse().join('')
  })
  var vm =  new Vue({
      el: '#app',
      data: {
          msg: 'world',
          number: 148547,
          filterParam: 'small text!',
          dataArray: [
            {'name': 'Bruce', 'age': 1},
            {'name': 'Chuck', 'age': 2, 'temp': 'Chuck123'},
            {'name': 'Jack', 'age': 3},
          ]
      }
  })
```