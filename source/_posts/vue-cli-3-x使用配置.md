---
title: vue-cli 3.x使用配置
date: 2018-12-24 20:33:39
tags: ['vue-cli 3', 'vue脚手架']
categories: ['前端工具']
---

### 安装

> Node 版本要求
> Vue CLI 需要 Node.js 8.9 或更高版本 (推荐 8.11.0+)。你可以使用 nvm 或 nvm-windows 在同一台电脑中管理多个 Node 版本。

```js
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```

#### 检查版本是否正确（3.x）

```js
vue --version
```

<!-- more -->

### 如果需要使用旧版本的 vue init 功能，可以全局安装一个桥接工具：

```js
npm install -g @vue/cli-init
```

### 创建项目
```js
vue create projectName
```

这里我选择自定义项目

![](/images/cli3/cli1.png)

选择自己项目中需要安装的包，选择后点击‘空格’选中，回车下一步：

![](/images/cli3/cli2.png)

输入‘Y’路由使用history模式

![](/images/cli3/cli3.png)

选择一个自己喜欢的css预处理器，这里我选择了stylus

![](/images/cli3/cli4.png)

选择eslint 标准

![](/images/cli3/cli5.png)

保存lint
![](/images/cli3/cli6.png)

选择将Babel,postCSS, ESLint配置在专用配置文件中，还是在pakage.json中。

![](/images/cli3/cli7.png)

### 启动项目

```js
npm run serve
```

### 启动后可能碰到的问题

* 问题：
  
  ![](/images/cli3/invalid.png)
  [解决方案](https://github.com/webpack/webpack-dev-server/issues/1604)


### 如何在webpack中添加的配置呢？

 1. 首先在项目最外层创建一个vue.config.js的文件，configureWebpack选项提供一个对象,该对象将会被 webpack-merge 合并入最终的 webpack 配置。

 ```js
 module.exports = {
  configureWebpack: config => {
    if (process.env.NODE_ENV === 'production') {
      // 为生产环境修改配置...
    } else {
      // 为开发环境修改配置...
    }
  }
}
 ```

 ### 我们习惯了再vue-cli 2.x中配置文件别名，那么在vue-cli 3.x中如何配置？

1. 还是在vue.config.js 文件中配置chainWebpack对象

```js
const path = require('path')

function resolve(dir) {
  return path.join(__dirname, dir)
}
module.exports = {
  chainWebpack: config => {
    config.resolve.alias
      .set('@', resolve('src'))
      .set('assets', resolve('src/assets'))
      .set('views', resolve('src/views'))
  }
}
```
[更多关于vue.config.js文件的配置](https://cli.vuejs.org/zh/config/#%E5%85%A8%E5%B1%80-cli-%E9%85%8D%E7%BD%AE)

### 总结

vue-cli 2.x的时候配置文件都暴露在项目中，对于多人协同开发的项目，开发人员有时候会自主根据自己的意愿进行修改，对于刚入手vue的人来说多人开发很容易把项目改乱，对于管理员维护不方便。以前使用2.x的时候，我都是把项目中的依赖、配置文件单独提出来，抽出一个单独的SDK打成npm包，发到线上。现在vue-cli 3.x已经把这些配置文件都抽成了npm包，同时只预留一些配置入口，着实方便不少，在项目中只需简单的配置就完成了一个项目搭建。