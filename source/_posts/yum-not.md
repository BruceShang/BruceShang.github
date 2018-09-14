---
title: yum操作常用命令
date: 2018-01-31 13:41:38
tags: [yum, linux]
categories: yum
---
## 列出所有已安装的软件包
```js
yum list installed 
```
### yum针对软件包操作常用命令： 

### 1.使用YUM查找软件包
```js
yum search 
```
### 2.列出所有可安装的软件包
```js
yum list 
```
### 3.列出所有可更新的软件包
```js
yum list updates 
```
<!--more-->
### 4.列出所有已安装的软件包
```js
yum list installed 
```
### 5.列出所有已安装但不在 Yum Repository 内的软件包
```js
yum list extras 
```
### 6.列出所指定的软件包
```js
yum list 
```
### 7.使用YUM获取软件包信息
```js
yum info 
```
### 8.列出所有软件包的信息
```js
yum info 
```
### 9.列出所有可更新的软件包信息
```js
yum info updates 
```
### 10.列出所有已安装的软件包信息
```js
yum info installed 
```
### 11.列出所有已安装但不在 Yum Repository 内的软件包信息
```js
yum info extras 
```
### 12.列出软件包提供哪些文件
```js
yum provides
```