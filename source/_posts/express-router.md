---
title: express 路由(Router)
tags: [Express, express.Router, node]
date: 2016-10-29 13:32:50
categories: Express
---


### 基本路由

1.路由用于确定应用程序如何响应对特定端点的客户机请求，包含一个 URI（或路径）和一个特定的 HTTP 请求方法（GET、POST 等）。
每个路由可以具有一个或多个处理程序函数，这些函数在路由匹配时执行。
路由定义采用以下结构：

```js
app.METHOD(PATH, HANDLER)

```
<!--more-->
其中：
app 是 express 的实例。
METHOD 是 HTTP 请求方法。
PATH 是服务器上的路径。
HANDLER 是在路由匹配时执行的函数。

2.在根路由 (/) 上（应用程序的主页）对 POST 请求进行响应：
```js
app.put('/user', function (req, res) {
  res.send('Got a PUT request at /user');
});
```
有一种特殊路由方法：app.all()，它并非派生自 HTTP 方法。该方法用于在所有请求方法的路径中装入中间件函数。
在以下示例中，无论您使用 GET、POST、PUT、DELETE 还是在 http 模块中支持的其他任何 HTTP 请求方法，都将为针对“/secret”的请求执行处理程序。

```js
app.all('/secret', function (req, res, next) {
console.log('Accessing the secret section ...');
next(); // pass control to the next handler
});
```
### 路由路径

路由路径与请求方法相结合，用于定义可以在其中提出请求的端点。路由路径可以是字符串、字符串模式或正则表达式。

Express 使用 path-to-regexp 来匹配路由路径；请参阅 path-to-regexp 文档以了解定义路由路径时所有的可能性。Express Route Tester 是用于测试基本 Express 路由的便捷工具，但是它不支持模式匹配。

以下是基于字符串的路由路径的一些示例。

此路由路径将请求与根路由 / 匹配。

```js
app.get('/', function (req, res) {
  res.send('root');
});
```
此路由路径将请求与 /about 匹配。

```js
app.get('/about', function (req, res) {
  res.send('about');
});
```
字符 ?、+、* 和 () 是其正则表达式同应项的子集。基于字符串的路径按字面理解连字符 (-) 和点 (.)。

### express.Router
使用 express.Router 类来创建可安装的模块化路由处理程序。Router 实例是完整的中间件和路由系统；因此，常常将其称为“微型应用程序”。

以下示例将路由器创建为模块，在其中装入中间件，定义一些路由，然后安装在主应用程序的路径中。

在应用程序目录中创建名为 birds.js 的路由器文件，其中包含以下内容：

```js
var express = require('express');
var router = express.Router();
// middleware that is specific to this router
router.use(function timeLog(req, res, next) {
  console.log('Time: ', Date.now());
  next();
});
// define the home page route
router.get('/', function(req, res) {
  res.send('Birds home page');
});
// define the about route
router.get('/about', function(req, res) {
  res.send('About birds');
});
module.exports = router;
```
接着，在应用程序中装入路由器模块：
```js
var birds = require('./birds');
...
app.use('/birds', birds);
```
此应用程序现在可处理针对 /birds 和 /birds/about 的请求，调用特定于此路由的 timeLog 中间件函数。
