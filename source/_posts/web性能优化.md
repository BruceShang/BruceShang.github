---
title: 前端性能优化
date: 2018-08-28 20:05:01
tags: [web性能]
categories: [ssh key]
---

## 前端性能优化

### 1.减少http请求 
- 1.图片
- 2.样式
- 3.脚本

### 2.压缩组件
> 前端
- 1.css文件 and js脚本的内容压缩，图片压缩
> 服务端
- 1.启用GZIP编码来压缩http响应包的大小。通过HTTP请求中的Accept-Encoding头来标识对压缩的支持

```js
Accept-Encoding: gzip,deflate
```
<!-- more -->
### 3.样式表放在顶部

### 4.将脚本放在底部

### 5.避免css表达式

例如：
```js
width:expression(setCntr(),document.body.clientWidth<600?"600px":"auto");
```

### 6.精简JavaScript
例如：避免使用过多额全局遍历，避免过多的dom操作,缓存查找到的DOM元素

### 7.配置ETag
ETag 全称为：Entity Tag，意思是实体标签。

作用是用一个特殊的字符串来标识某个资源的“版本”，客户端（浏览器）来请求的时候，可以比较，如果ETag一致，则表示该资源并没有修改过，客户端（浏览器）可以使用自己缓存的版本。
最终目的：缓存内容减少HTTP请求。

### 8.使Ajax可缓存
使Ajax可缓存的前提是保证你的数据是不经常变化的，如果发生变化就重新从服务器请求新的数据

关于Ajax，我建议通过Ajax请求的内容仅包含必须从服务端返回的数据信息，HTML结构部分则缓存到HTML页面当中，然后通过模版解析，把所通过ajax请求的内容展示出来，因为目前浏览器端的运算速度越来越快了，所以现在完全可以这样做。

### 9.http缓存分类

- 强制缓存
> 在缓存数据未失效的情况下，不需要再和服务器发生交互
#### Expires
  我们在demo.js中添加了一个Expires响应头，不过由于是格林尼治时间，所以通过momentjs转换一下。第一次请求的时候还是会向服务器发起请求，同时会把过期时间和文件一起返回给我们；但是当我们刷新的时候，才是见证奇迹的时刻：
可以看出文件是直接从缓存（memory cache）中读取的，并没有发起请求。我们在这边设置过期时间为两分钟，两分钟过后可以刷新一下页面看到浏览器再次发送请求了。
  虽然这种方式添加了缓存控制，节省流量，但是还是有以下几个问题的：

由于浏览器时间和服务器时间不同步，如果浏览器设置了一个很后的时间，过期时间一直没有用
缓存过期后，不管文件有没有发生变化，服务器都会再次读取文件返回给浏览器

  不过Expires 是HTTP 1.0的东西，现在默认浏览器均默认使用HTTP 1.1，所以它的作用基本忽略。

#### Cache-Control
针对浏览器和服务器时间不同步，加入了新的缓存方案；这次服务器不是直接告诉浏览器过期时间，而是告诉一个相对时间Cache-Control=10秒，意思是10秒内，直接使用浏览器缓存。

```js
app.get('/demo.js',(req, res)=>{
    let jsPath = path.resolve(__dirname,'./static/js/demo.js');
    let cont = fs.readFileSync(jsPath);
    res.setHeader('Cache-Control', 'public,max-age=120') //2分钟
    res.end(cont)
})

```
- 协商缓存
需要进行比较判断是否可以使用缓存

强制缓存的弊端很明显，即每次都是根据时间来判断缓存是否过期；但是当到达过期时间后，如果文件没有改动，再次去获取文件就有点浪费服务器的资源了。

#### Last-Modified
为了节省服务器的资源，再次改进方案。浏览器和服务器协商，服务器每次返回文件的同时，告诉浏览器文件在服务器上最近的修改时间。请求过程如下：

浏览器请求静态资源demo.js
服务器读取磁盘文件demo.js，返给浏览器，同时带上文件上次修改时间 Last-Modified（GMT标准格式）
当浏览器上的缓存文件过期时，浏览器带上请求头If-Modified-Since（等于上一次请求的Last-Modified）请求服务器
服务器比较请求头里的If-Modified-Since和文件的上次修改时间。如果果一致就继续使用本地缓存（304），如果不一致就再次返回文件内容和Last-Modified。
循环请求。。
```js
app.get('/demo.js',(req, res)=>{
    let jsPath = path.resolve(__dirname,'./static/js/demo.js')
    let cont = fs.readFileSync(jsPath);
    let status = fs.statSync(jsPath)

    let lastModified = status.mtime.toUTCString()
    if(lastModified === req.headers['if-modified-since']){
        res.writeHead(304, 'Not Modified')
        res.end()
    } else {
        res.setHeader('Cache-Control', 'public,max-age=5')
        res.setHeader('Last-Modified', lastModified)
        res.writeHead(200, 'OK')
        res.end(cont)
    }
})

```
虽然这个方案比前面三个方案有了进一步的优化，浏览器检测文件是否有修改，如果没有变化就不再发送文件；但是还是有以下缺点：

由于Last-Modified修改时间是GMT时间，只能精确到秒，如果文件在1秒内有多次改动，服务器并不知道文件有改动，浏览器拿不到最新的文件
如果服务器上文件被多次修改了但是内容却没有发生改变，服务器需要再次重新返回文件。

#### ETag


浏览器请求静态资源demo.js
服务器读取磁盘文件demo.js，返给浏览器，同时带上文件的唯一标识ETag
当浏览器上的缓存文件过期时，浏览器带上请求头If-None-Match（等于上一次请求的ETag）请求服务器
服务器比较请求头里的If-None-Match和文件的ETag。如果一致就继续使用本地缓存（304），如果不一致就再次返回文件内容和ETag。
循环请求。。
```js
const md5 = require('md5');

app.get('/demo.js',(req, res)=>{
    let jsPath = path.resolve(__dirname,'./static/js/demo.js');
    let cont = fs.readFileSync(jsPath);
    let etag = md5(cont);

    if(req.headers['if-none-match'] === etag){
        res.writeHead(304, 'Not Modified');
        res.end();
    } else {
        res.setHeader('ETag', etag);
        res.writeHead(200, 'OK');
        res.end(cont);
    }
})

```
### 缓存的优先级
两种缓存规则可以同时存在，强制缓存优先级高于协商缓存。
Pragma > Cache-Control > Expires > ETag > Last-Modified
![](/images/etag.png)
参考资料

[前端也要懂Http缓存机制](https://juejin.im/post/5b70edd4f265da27df0938bc)
