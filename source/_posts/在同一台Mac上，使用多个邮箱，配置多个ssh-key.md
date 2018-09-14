---
title: 在同一台Mac上，使用多个邮箱，配置多个ssh key
date: 2018-07-31 19:34:57
tags: [ssh key]
categories: [ssh key]
---

## 前言介绍

> 自己开始用自己的邮箱，注册了ssh key，将公钥添加到了github上，但现在公司要求使用企业邮箱生成公钥，将使用公司的企业邮箱生成的公钥添加到自己公司搭建的gitlab私服上，由此以来开始了下面的折腾。同时使用多个邮箱，注册多个公钥。

### 1.先使用自己的邮箱生成第一个key
```js
// 在终端中执行
ssh-keygen -t rsa -C "yourEmail@xxx.com"
```
> 第一个key一路回车,生成以后，在终端中执行下面命令进行查看

```js
open ~/.ssh
```
- 此时，.ssh文件中多个两个文件 id_rsa 私钥，id_rsa.pub

### 2.在使用公司邮箱生成第二个key
```js
// 在终端中执行
ssh-keygen -t rsa -C "companyEmail@xxx.com"
```
> 回车以后：在下面的操作中，给第二个key取一个名字，用于区分。如果一路回车会覆盖掉第一次生成的秘钥。

```js
Enter file in which to save the key (/Users/Shinancao/.ssh/id_rsa): id_rsa_customName
```
- 现在，再执行命令 open ~/.ssh命令，我们能看到 .ssh文件中又多了两个秘钥
<!-- more -->
### 3.在.ssh文件夹中创建一个config文件

```js
vim ~/.ssh/config
```
> 配置config

```js
# github

Host github
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa
User github注册的用户名

# custome-gitlab

Host company // 别名
HostName 192.168.11.XXX // 公司服务器ip
port 10080 // 端口号
PreferredAuthentications publickey // 强制使用Public Key验证
IdentityFile ~/.ssh/id_rsa_nyso // 密钥文件的路径
User 注册的用户名
```

### 4.使用的过程会报错，那是因为我们还没有加载刚才的配置

```js
//查看当前rsa list
ssh-add -l 

//如果列表中没有新增的rsa， 添加identifile
ssh-add ~/.ssh/id_rsa
ssh-add ~/.ssh/id_rsa_customName
// 再去操作就没有问题了
```

### 5.设置全局的用户名和邮箱

```js
git config --global user.name "your name"
git config --global user.email "your email"
```

### 6.查看配置的结果

```js
git config --global user.name
git config --global user.email
```

### 7.设置全局后默认都是，全局配置用户名和邮箱。如果我们想针对不同的仓库，使用不同的用户名和邮箱呢？

```js
// 先取消全局设置
git config --global --unset user.name
git config --global --unset user.email

// 再进入项目目录下设置
git config user.email "your name"
git config user.email "your email"

// 查看配置结果
git config user.name
git config user.email
```

### 8.大功告成，可以下班回家喽。😁