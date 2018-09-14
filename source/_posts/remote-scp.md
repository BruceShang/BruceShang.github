---
title: mac电脑终端使用scp上传/下载文件/文件夹
tags: [scp, mac scp]
date: 2017-11-15 18:03:08
categories: scp
---

##### 1.从服务器下载文件到本地电脑

```js
scp -r remote_username@remote_ip:remote_folder local_folder
```

例如：

```js
scp -r root@106.xx.xxx.xx:/root/www/file /Desktop/folder
```

##### 2.从本地上传文件到服务器

```js
scp -r local_folder remote_username@remote_ip:remote_folder
```
例如：

```js
scp -r /Desktop/folder root@106.xx.xxx.xx:/root/www/file
```

##### 3.上传和下载文件就是把这种格式来回调换一下
