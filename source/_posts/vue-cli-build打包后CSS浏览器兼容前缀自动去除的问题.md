---
title: vue-cli build打包后CSS浏览器兼容前缀自动去除的问题
date: 2018-09-25 09:54:42
tags: ['vue-cli', 'h5中ios8兼容问题', 'vue-cli打包后css前缀自动去除问题' ]
categories: ['vue-cli']
---

今天做练手项目部署的时候碰到了个奇怪问题，开发环境里显示得好好的CSS prefixer样式，到production环境就消失了。
我开发环境用的是 scss ，刚开始我还以为是 scss 处理器的问题，后来研究了下 build 脚本，发现人家把CSS统一用一个[插件](https://github.com/NMFR/optimize-css-assets-webpack-plugin)给压缩了，然后追查之下发现这个插件用了 [postCSS](https://postcss.org/) 的 [autoprefixer](https://github.com/postcss/autoprefixer) 插件。
这下问题就清楚了，因为 autoprefixer 插件会针对支持的浏览器进行CSS 前缀的删除和追加。

比如：

```css
a {
  -webkit-border-radius: 5px;
          border-radius: 5px;
}
```

编译成：

<!-- more -->

```css
a {
  border-radius: 5px;
}
```

autoprefixer 使用了 [browserslist](https://github.com/browserslist/browserslist) 作为依赖。在项目目录中运行:

```js
npx browserslist

```

![](/images/browserslist.png)
可以查看当前项目支持的浏览器列表，这时候只要修改当前项目支持的浏览器就可以了。
vue-cli生成的项目默认支持部分移动端浏览器和最新版本的PC端浏览器。

在 <strong>package.json</strong> 中修改 <code>browserslist</code>

```js
"browserslist": [
  "> 1%",
  "last 2 versions",
  "last 10 Chrome versions",
  "last 5 Firefox versions",
  "Safari >= 6",
  "ie > 8"
]
```
如果是h5移动端项目中需要兼容iPhone 5 （ios8）的需要如下配置：

```js
"browserslist": [
  "> 1%",
  "last 2 versions",
  "last 10 Chrome versions",
  "last 5 Firefox versions",
  "Safari >= 6",
  "ie > 8",
  "ios >= 7",
  "android >= 4.3"
],
```

之后再运行 <code>npm run build</code> 这时生成的代码就有 css prefix 了。

博客原文：[http://www.zhuyuwei.cn/2018/vue-cli-css-prefixer.html](http://www.zhuyuwei.cn/2018/vue-cli-css-prefixer.html)