---
title: weex 配置安卓开发环境
date: 2019-01-31 19:32:36
tags: ['weex']
categories: ['weex', 'native']
---

## 1.安装依赖

安装 Node.js 方式多种多样，最简单的方式是在 Node.js 官网 下载可执行程序直接安装即可。

对于 Mac，可以使用 Homebrew 进行安装：

```js
brew install node
```
通常，安装了 Node.js 环境，npm 包管理工具也随之安装了。因此，直接使用 npm 来安装 weex-toolkit。

```js
npm install -g weex-toolkit
```
<!-- more -->
## 2.初始化weex项目

```js
// 1.生成项目
weex create awesome-app
// 2.进入项目
cd awesome-app
// 3.安装依赖
npm install
// 4.启动项目
npm start
```

## 3.调试查看网络请求信息

weex-toolkit 还提供了强大的调试功能，只需要执行：

```js
weex debug
```
[调试详情步骤](https://weex.apache.org/cn/tools/toolkit.html)

## 4.集成weex到应用

- 1.安装安卓运行环境[JDK](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- 2.安装Android SDK并配置环境变量
- - 这里我是下载了[Android Studio](https://developer.android.com/studio/)编辑器，打开软件的时候在内部会自动安装，只需要耐心等一下。


## 5.打包安装
```js
weex run android
```
## 6.打包$ANDROID_HOME not found !问题
这是没有将sdk配置到环境变量中

```js
// 进入配置文件
vi ~/.zshrc

// 将下面代码放入文件中
export ANDROID_HOME=/Users/shangzhen/Library/Android/sdk
export  PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools
```
这是sdk的安装位置，在这里可以看到。
![](/images/SDK-position.png)
最后千万别忘了最后一步
```js
source ~/.zshrc
```
配置好之后一定要重新开一个终端执行命令

## 7.Build failed with an exception 问题解决
```js
// 报错信息
FAILURE: Build failed with an exception.

* What went wrong:
A problem occurred configuring project ':app'.
> You have not accepted the license agreements of the following SDK components:
  [Android SDK Platform 23, Android SDK Build-Tools 23.0.2].
  Before building your project, you need to accept the license agreements and complete the installation of the missing components using the Android Studio SDK Manager.
  Alternatively, to learn how to transfer the license agreements from one workstation to another, go to http://d.android.com/r/studio-ui/export-licenses.html
```
执行命令解决
```js
$ANDROID_HOME/tools/bin/sdkmanager --licenses
```
看到这个页面就代表许可证安装完成了
![](/images/weex-licenses.png)

## 8.执行 weex run android

用一个安卓手机，连接到自己的电脑，手机上开启开发调试模式。
编译通过，
![](/images/weex-success.png)
打包安装到真机上的APP效果图：
![](/images/weex-success-iphone.png)

