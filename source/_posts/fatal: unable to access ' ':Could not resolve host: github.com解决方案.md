---
title: "fatal: unable to access ' ':Could not resolve host: github.com解决方案"
date: 2019-06-05 11:45:38
tags: ['git']
categories: ['git']
---

## 一、在终端输入：

```js
sudo vim /etc/resolv.conf
```
在最后一行增加nameserver 8.8.8.8或者nameserver 114.114.114.114即可。

<!-- more -->

> 114.114.114.114和8.8.8.8，这两个IP地址都属于公共域名解析服务DNS其中的一部分

## 二、114.114.114.114

114.114.114.114是国内移动、电信和联通通用的DNS，手机和电脑端都可以使用，干净无广告，解析成功率相对来说更高，国内用户使用的比较多，而且速度相对快、稳定，是国内用户上网常用的DNS。

## 三、8.8.8.8

　8.8.8.8是GOOGLE公司提供的DNS，该地址是全球通用的，相对来说，更适合国外以及访问国外网站的用户使用。

