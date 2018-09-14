---
title: 构建自己的npm包
date: 2018-07-10 10:24:21
tags: [npm]
categories: [npm]
---

## 为什么要使用NPM

> NPM是随同NodeJS一起安装的包管理工具，能解决NodeJS代码部署上的很多问题，常见的使用场景有以下几种：

* 允许用户从NPM服务器下载别人编写的第三方包到本地使用。
* 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用。
* 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用。

> 由于新版的nodejs已经集成了npm，所以之前npm也一并安装好了。同样可以通过输入 "npm -v" 来测试是否成功安装。命令如下，出现版本提示表示安装成功:


```js
5.6.0
```
<!-- more -->
### 1. 先在本地构建一个demo的项目，创建index.js文件
```js
// index.js
module.exports.helloWorld = function() {
    return 'Hello World!'
}
```
### 2. npm 初始化创建package.json文件

```js
npm init
```
![](/images/npm-init.png)



### 3. 先在[npmjs](https://www.npmjs.com/)官网上注册一个自己的账号

> 注册成功后，打开命令行工具。
```js
npm login
```
> 根据命令提示输入用户名密码，登录后可以查询自己的账号

```js
npm whoami
```
![](/images/npm-login.png)

### 4. 这些做完准备工作就都准备完了，现在开始上传npm包

> cmd进入demo目录，执行下面命令
```js
npm publish
```

如果第一次名称没有冲突和符合npm命名规范的话，就直接上传成功了

> 特别注意，每次发包都要更改版本号

### 5.使用npm包

```js
// 先安装刚刚上传的包
npm i test-package-name --save-dev
// 再引用
let test = require('test-package-name')
```

### 6.定义作用于模块

> 防止构建新包命名时和别人的重复，造成发布失败。我们使用自己的用户名定义一个作用域，每个npm用户都有一个以自己名为作用域。

```js
@username/project-name
```

### 7.发布作用模块

```js
@username/project-name
```
- 报错了，什么鬼。

![](/images/npm-error.png)

> 原来携带头的npm作用域默认发的是私有包，要想成功发包，添加access=public参数，发布成为公用包

```js
npm publish --access=public
```