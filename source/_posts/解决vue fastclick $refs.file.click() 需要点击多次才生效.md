---
title: 解决vue fastclick $refs.file.click() 需要点击多次才生效
date: 2020-04-15 20:45:23
tags: ['vue', 'fastclick', 'file.click']
categories: ['vue', 'js']
---

### 项目背景

1.移动端h5项目
2.项目中使用了fastclick
3.h5页面需要用户上传身份证图片

### 实现原理

fastclick是通过在touchstart里记录触摸点信息，在touchend里判断此次触摸是否是个有效的click点击，如果是，则在touchend里阻止默认事件以防止产生原生click事件（因为会有 300ms 的延迟），并立即生成并触发自定义的click事件（没有延迟）

<!-- more -->

### 哪些情况不需要 fastclick

在代码里引入fastclick后，初始化fastclick时会做一些检测，当检测出如下情况时，将不会使用fastclick：

- 设备不支持touch事件
- Chrome 浏览器
  - 桌面版 Chrome 浏览器
  - Android Chrome，以下两种情况都不需要fastclick
  - eta里有user-scalable=no
  - Chrome 版本 >= 32，且meta里有width=device-width
- IE 浏览器
  - IE 11+，传入fastclick.attach的元素包含 CSS 样式，touch-action: manipulation;
  - IE 10，传入fastclick.attach的元素包含 CSS 样式，-ms-touch-action: manipulation;
- BlackBerry 浏览器
  - 版本 >= 10.3，且meta里有user-scalable=no或width=device-width
- Firefox 浏览器
  - 版本 >= 27，且meta里有user-scalable=no或width=device-width
  

### 存在的问题

手动触发 element.click() 无效

div1，点击此处，将在 div1 的 click 回调里触发 div2.click()

div2，div2 的 click 回调里将 tootle 背景色

#### 问题描述
可能存在这样的需求，点击元素div1，在div1元素的click回调函数里再以代码方式触发另一元素div2的click事件。

```html
<template>
    <div class="fastclick-bug">
        <div
            class="div1"
            @click="clickDiv1"
        >
            div1，点击此处，将在 div1 的 click 回调里触发 div2.click()
        </div>
        <div
            ref="div2"
            class="div2"
            :class="{'div2-bg': div2Bg}"
            @click="clickDiv2"
        >
            div2，div2 的 click 回调里将 tootle 背景色
        </div>
    </div>
</template>

<script>
export default {
    name: 'VueTapFastclickBug',
    data() {
        return {
            div2Bg: false
        };
    },
    mounted() {
        // 在浏览器端再执行 fastclick
        import('fastclick').then(Fastclick => {
            Fastclick.attach(document.body);
        });
    },
    methods: {
        clickDiv1() {
            this.$refs.div2.click();
        },
        clickDiv2() {
            this.div2Bg = !this.div2Bg;
        }
    }
};
</script>

<style lang="less" scoped>
.div1,
.div2 {
    margin: 20px;
    background: gray;
}
.div2-bg {
    background: red;
}
</style>
```

若是使用了fastclick，但Android上可以按预期运行（实际上是这个Android手机的 Chrome 版本 >= 32，且meta里有width=device-width，没使用到fastclick）。

在 iOS 上，this.$refs.div2.click();这一行代码不会生效，但将这一行代码写两遍，即手动触发div2.click()两次，代码就会按预期运行。

#### 问题原因

当点击屏幕时，fastclick在touchstart里会设置fastclick实例的targetElement属性为当前事件的目标元素。

```js
FastClick.prototype.onTouchStart = function(event) {
  var targetElement, touch, selection;

  if (event.targetTouches.length > 1) {
    return true;
  }
  // 事件目标元素
  targetElement = this.getTargetElementFromEventTarget(event.target);
  // ...
  // 设置 fastclick 实例的 targetElement 属性为当前事件的目标元素
  this.targetElement = targetElement;
  // ...
  return true;
};
```

但是通过this.$refs.div2.click();是在 js 里以编程方式触发click事件，不会产生touchstart事件，因此无法重新设置this.targetElement，而且在点击了div1之后，this.targetElement也没有重置为null，其值仍为div1。

```js

function FastClick(layer, options) {
  // ...
  var methods = ['onMouse', 'onClick', 'onTouchStart', 'onTouchMove', 'onTouchEnd', 'onTouchCancel'];
  var context = this;
  for (var i = 0, l = methods.length; i < l; i++) {
    context[methods[i]] = bind(context[methods[i]], context);
  }

  // Set up event handlers as required
  if (deviceIsAndroid) {
    layer.addEventListener('mouseover', this.onMouse, true);
    layer.addEventListener('mousedown', this.onMouse, true);
    layer.addEventListener('mouseup', this.onMouse, true);
  }

  // 注意，click 事件是在捕获阶段监听处理的
  layer.addEventListener('click', this.onClick, true);
  layer.addEventListener('touchstart', this.onTouchStart, false);
  layer.addEventListener('touchmove', this.onTouchMove, false);
  layer.addEventListener('touchend', this.onTouchEnd, false);
  layer.addEventListener('touchcancel', this.onTouchCancel, false);
  // ...
}
```

第一次div2.click()时，会在事件捕获阶段被layer（一般是document.body）上的监听函数处理，即this.onClick。在onClick里会调用onMouse，onMouse里通过needsClick判断出this.targetElement即div1不需要原生click事件，因此会阻止该事件的默认行为以及阻止事件冒泡/捕获，并将this.targetElement置为null。因此第一次的div2.click()在事件捕获阶段被layer的事件处理函数给阻止了捕获阶段的进步传播，最终事件未传播到div2上，div2的click事件也未执行。

因为在第一次div1.click()时已经将this.targetElement重置为null了，第二次div2.click()时在onMouse里判断出this.targetElement为null后直接返回true，而没有机会执行之后的needsClick以及阻止事件捕获的逻辑，事件会进一步传播到div2上，因此第二次的div2.click()生效了。

```js
FastClick.prototype.needsClick = function(target) {
  switch (target.nodeName.toLowerCase()) {

  // Don't send a synthetic click to disabled inputs (issue #62)
  case 'button':
  case 'select':
  case 'textarea':
    if (target.disabled) {
      return true;
    }

    break;
  case 'input':

    // File inputs need real clicks on iOS 6 due to a browser bug (issue #68)
    if ((deviceIsIOS && target.type === 'file') || target.disabled) {
      return true;
    }

    break;
  case 'label':
  case 'iframe': // iOS8 homescreen apps can prevent events bubbling into frames
  case 'video':
    return true;
  }

  return (/\bneedsclick\b/).test(target.className);
};

FastClick.prototype.onMouse = function(event) {

  // If a target element was never set (because a touch event was never fired) allow the event
  if (!this.targetElement) {
    return true;
  }

  if (event.forwardedTouchEvent) {
    return true;
  }

  // Programmatically generated events targeting a specific element should be permitted
  if (!event.cancelable) {
    return true;
  }

  // Derive and check the target element to see whether the mouse event needs to be permitted;
  // unless explicitly enabled, prevent non-touch click events from triggering actions,
  // to prevent ghost/doubleclicks.
  if (!this.needsClick(this.targetElement) || this.cancelNextClick) {

    // Prevent any user-added listeners declared on FastClick element from being fired.
    // 阻止事件冒泡并且阻止相同事件的其他侦听器被调用
    if (event.stopImmediatePropagation) {
      event.stopImmediatePropagation();
    } else {

      // Part of the hack for browsers that don't support Event#stopImmediatePropagation (e.g. Android 2)
      event.propagationStopped = true;
    }

    // Cancel the event
    event.stopPropagation();
    event.preventDefault();

    return false;
  }

  // If the mouse event is permitted, return true for the action to go through.
  return true;
};

FastClick.prototype.onClick = function(event) {
  // ...
  permitted = this.onMouse(event);
  // ...
  // Only unset targetElement if the click is not permitted. This will ensure that the check for !targetElement in onMouse fails and the browser's click doesn't go through.
  // 若不允许此次 click 事件，则重置 this.targetElement
  if (!permitted) {
    this.targetElement = null;
  }

  // If clicks are permitted, return true for the action to go through.
  return permitted;
};
```

PS：DOM Level 2 里事件处理函数handler里的return false即不会阻止默认事件，也不会阻止冒泡。


#### 解决方案

##### 方案一：添加 needsclick 类（不推荐）

```html
<div
    class="div1 needsclick"
    @click="clickDiv1">
    div1，点击此处，将在 div1 的 click 回调里触发 div2.click() 
</div>
```

在div1元素上添加needsclick类，这样点击div1时不会触发fastclick自定义的click事件，而是原生click事件（会存在 300 ms 延迟的问题）。


且仅需要执行一次div2.click()，执行时needsclick函数根据div1上的needsclick判断出this.targetElement即div1需要原生click事件，就不会阻止事件的进一步传播，最终div2上的click事件回调顺利执行。

##### 方案二：使用 element.dispatchEvent(event) 触发事件（推荐）

```js
clickDiv1() {
    const event = new MouseEvent('click');
    this.$refs.div2.dispatchEvent(event);
},
```

通过new MouseEvent('click')创建的事件，默认的event.cancelable和event.cancelBubble都为false，因此走到onMouse里会直接返回true，没有机会执行之后的needsClick以及阻止事件捕获的逻辑，会让事件进一步传播到div2上，进而触发了div2的click事件。

注意：div2上的事件，不能是采用事件代理注册在其祖先元素上的click事件.