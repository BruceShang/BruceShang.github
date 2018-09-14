---
title: nvm常用操作
tags: [nvm, mac nvm]
date: 2017-11-17 18:17:01
categories: nvm
---

##### 1.安装nvm命令
[github.com nvm](https://github.com/creationix/nvm)

> wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.6/install.sh | bash

##### 2.核实nvm是否安装成功，安装成功后执行命令返回’nvm’

```js
command -v nvm
```

##### 3.检查当前支持什么版本
```js
nvm ls-remote
```
<!--more-->
##### 4.指定安装版本
```js
nvm install vx.x.x
```

##### 5.安装最新稳定版本
```js
nvm install stable
```

##### 6.删除某版本的node

```js
nvm uninstall 4.4.0
nvm uninstall default
```
##### 7.nvm使用default的alias来实现默认版本,执行命令
```js
nvm alias default vx.x.x
```

##### 8.查看当前已经安装的版本
```js
nvm ls
```

##### 9.以指定版本执行脚本
```js
nvm use v8.8.1 app.js
```

##### 10.卸载nvm
```js
rm -rf ~/.nvm
```

##### 11.切换至x.x.x版本
```js
nvm use vx.x.x
```
##### 12.查看正在使用的版本
```js
nvm current
```