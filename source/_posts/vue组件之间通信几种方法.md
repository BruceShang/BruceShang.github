---
title: vue组件之间通信几种方法
date: 2020-03-31 19:52:01
tags: ['vue', '组件通信']
categories: ['js']
---

### 跨组件【订阅/发布】模式

描述：vue2.0支持

```js
var bus = new Vue();
//把值发送指定频道
bus.$emit('fcName',1)

//接收指定频道的值
bus.$on('fcName',function(id){
	console.log(id);
});
```

<!-- more -->

### 通过prop向子组件通信

```html
<div id="app">
  <p>{{ total }}</p>
  <button type="button" @click="clickRef">调用子组件</button>
  <child :message="total"></child>
</div>
```

> message前加":" total是动态数据，未加total是静态数据

```jsx
Vue.component('child',{
    template:'<div>全局组件：{{ message }}</div>',
    props: {
    	//允许值类型，用数组表示
    	message: [String, Number]
    }
});
var vm = new Vue({
    el: '#app',
    data: {
      total: 1
    },
    methods: {
      clickRef: function(){
        this.total++;
      }
    }
});

```

### 子组件调用父组件

```html
<div id="app">
  <p>{{ total }}</p>
  <simple @increment = "parentFn"></simple>
</div>
```
```js
 //定义全局组件
  Vue.component('simple',{
    template: '<button @click="fns">子组件click</button>',
    methods: {
      fns: function(){
        this.$emit('increment');
      }
    }
  });

  var vm = new Vue({
    el: '#app',
    data: {
      total: 1
    },
    methods: {
      //在子组件调用emit() 来触发。
      parentFn: function(){
        this.total++;
      }
    }
  });
  ```

  > 备注：该方式主要通过子组件emit()方法来实现。

  ### 父组件调子组件方法和数据

  ```html
  <div id="app">
    <p>总次数：{{ total }}</p>
    <button @click="parentClick">调子组件</button>
    <simple ref="children"></simple>
  </div>
```

```js
//定义全局组件
  Vue.component('simple',{
    template: '<button @click="fns">子组件次数({{ num }})</button>',
    data: function(){
      return { num: 0}
    },
    methods: {
      fns: function(){
        this.num++;
      }
    }
  });
  
  //实例化Vue对象
  var vm = new Vue({
    el: '#app',
    data: {
      total: 1
    },
    methods: {
      //在子组件调用emit() 来触发。
      parentClick: function(){
          this.total++;
          var child = this.$refs.children;
          child.fns();
      }
    }
  });
  ```

### 使用v-model进行通信

```js
  Vue.component('my-component', {
    template: `<div>{{currentVal}}这是：，<button @click="foo">传递</button></div>`,
    props: ['value'], // 必须
    data() {
      return {
        currentVal: this.value // 必须
      }
    },
    // 必须
    watch: {
      currentVal(val) {
        this.$emit('input', val)
      },
      value(val){
        this.currentVal = val
      }
    },
    methods: {
      foo() {
        this.currentVal++
      },
    },
  })

  var appMain = new Vue({
    el: '#app',
    data: {
      num: 3,
    },
    methods: {
      clickMe() {
        this.num ++
      }
    }
  })
  ```
  html

  ```html
  <div id="app">
    <p>总次数：{{ total }}</p>
    <button @click="parentClick">调子组件</button>
    <simple ref="children"></simple>
  </div>
```

[转载: Y. Jer 的虚拟笔记](https://www.isyxf.com/articles/vue--component-message)