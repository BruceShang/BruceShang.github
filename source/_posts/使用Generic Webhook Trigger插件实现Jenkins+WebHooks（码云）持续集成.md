---
title: 使用Generic Webhook Trigger插件实现Jenkins+WebHooks（码云）持续集成
date: 2019-05-09 15:00:00
tags: ['Jenkins']
categories: ['前端工具']
---

## 一、下载插件安装Generic Webhook Trigger插件

在“系统设置–插件管理–可选插件”界面搜索：Generic Webhook Trigger，可以看到，点击安装，然后重启。

![](/images/jenkins/webhook-plugin.png)

<!-- more -->

## 二、构建触发

![](/images/jenkins/trriger.png)

## 三、配置用户和Security

![](/images/jenkins/token.png)

## 四、配置码云WebHooks

> 找到WebHooks配置界面，在POST地址框中输入如下格式内容：

```js
http://<User ID>:<API Token>@<Jenkins IP地址>:端口/generic-webhook-trigger/invoke 
```
```js
// 例：
http://admin:1156a09fc1c858c800a9e88a2c276@107.12.84.144:8082/generic-webhook-trigger/invoke
```
- User ID：既是“三（1）”中的User ID，我的是admin
- API Token：既是“三（1）”中的API Token
- Jenkins IP地址：就是你的Jenkins的服务器IP地址，这个必须是公网IP地址。
- 端口：就是Jenkins服务器的端口

测试接口成功

![](/images/jenkins/testhooks.png)
自动发布成功
![](/images/jenkins/webhook-success.png)

#### [本文参考文章](https://blog.csdn.net/weixin_39706415/article/details/84585748)

## Jenkins系列

#### [1.使用Jenkins搭建前端构建环境](/2019/01/04/使用Jenkins搭建前端构建环境/)
#### [2.Jenkins安装插件动态获取git分支](/2019/05/09/Jenkins安装插件动态获取git分支/)
#### [3.使用Generic Webhook Trigger插件实现Jenkins+WebHooks（码云）持续集成](/2019/05/09/使用Generic%20Webhook%20Trigger插件实现Jenkins+WebHooks（码云）持续集成/)


