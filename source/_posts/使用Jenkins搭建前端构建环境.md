---
title: 使用Jenkins搭建前端构建环境
date: 2019-01-04 14:01:41
tags: ['Jenkins']
categories: ['前端工具']
---

## 前期准备

1.CentOS服务器
2.Jenkins
3.github账号
4.Nginx

## 这里Nginx的配置就不多说了，有兴趣的话可以参考前面的文章

[同台服务器上nginx配置多个服务站点共用80端口](http://localhost:4000/2018/12/26/%E5%90%8C%E5%8F%B0%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8Anginx%E9%85%8D%E7%BD%AE%E5%A4%9A%E4%B8%AA%E6%9C%8D%E5%8A%A1%E7%AB%99%E7%82%B9%E5%85%B1%E7%94%A880%E7%AB%AF%E5%8F%A3/)

<!-- more -->

## 服务器环境配置

> [先将Jenkins网站中的Jenkins存储库添加到包管理器中](https://jenkins.io/doc/book/installing/)

```js
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
```

> 下载安装java和git依赖包

```js
yum install java
yum install git
```

## 登录服务器生成ssh key

```js
// 设置提交用户名和邮箱
git config --global user.name "xxx"
git config --global user.email "xxxx@qq.com"
// 开始生成key
ssh-keygen -t rsa -C "yourEmail@xxx.com"
```
一路回车后就成功生成了
![](/images/jenkins/4.png)

##### 测试一下ssh是否能成功使用

```
ssh github.com
```
出现下面情况代表可以正常使用
![](/images/jenkins/6.png)

最后：拷贝服务器中的公钥添加到github中

## 什么是[jenkins](https://jenkins.io/doc/#what-is-jenkins)呢?

> Jenkins是一个独立的开源自动化服务器，可用于自动执行与构建，测试，交付或部署软件相关的各种任务。
Jenkins可以通过本机系统软件包，Docker安装，甚至可以由安装了Java Runtime Environment（JRE）的任何机器独立运行。

## 安装Jenkins

```js
yum install jenkins
```
## Jenkins配置

```js
// 进入Jenkins配置文件地址：
vim /etc/sysconfig/jenkins
```

##### Jenkins配置文件中的几个重要的参数

```js
JENKINS_HOME="/var/lib/jenkins" // Jenkins存储配置和工作的目录
JENKINS_USER="root" // 将字段值修改root权限
// 注意：Jenkins默认的端口号是8080，需要修改的可以在这里修改
JENKINS_PORT="8082" // Jenkins端口号
```

## 启动Jenkins

```
service jenkins restart
```

## 启动成功后我们在浏览器中输入ip地址和端口号，进入解锁页面

![](/images/jenkins/pwd.png)

## 输入密码后，继续进入下一步，进入新手页面。

> 选择第一个推荐插件安装
![](/images/jenkins/new-page.png)

## 安装中...

![](/images/jenkins/install.png)


## 创建管理员用户

![](/images/jenkins/1.png)

## 实例配置

![](/images/jenkins/2.png)

## 创建任务

![](/images/jenkins/3.png)

## 构建一个自由风格的软件项目

![](/images/jenkins/new-task.png)

## 安装两个插件

系统管理>插件管理>可选插件中搜索并安装，安装成功后需要重启

```js
// 部署项目时需要执行node安装依赖
1.NodeJS Plugin 
// 实现jenkins将服务器构建打包好的war包自动发布到应用服务器，并部署运行
2.Publish Over SSH
```
## 进入 系统管理 => 系统设置页面配置

##### Jenkins Location
Jenkins服务器地址
![](/images/jenkins/17.png)

	
## Publish over SSH配置
[Publish over SSH](https://wiki.jenkins.io/display/JENKINS/Publish+Over+SSH+Plugin)

![](/images/jenkins/18.png)

这里有一点容易出错：
Jenkins服务器（A）
应用服务器（B）

应用服务器（B）上创建~/.ssh文件夹和~/.ssh/authorized_keys文件，并将jenkins服务器(A)的公钥id_rsa.pub中的内容复制到authorized_keys文件

应用服务器上重启ssh服务，service sshd restart

现在jenkins服务器可免密码直接登陆应用服务器

## 开始任务配置
![](/images/jenkins/12.png)

## 构建环境

![](/images/jenkins/13.png)

## 构建
![](/images/jenkins/14.png)

## 选择Send build artifacts over SSH配置
![](/images/jenkins/16.png)
## 构建后操作

![](/images/jenkins/15.png)

## 测试一下部署发布

![](/images/jenkins/19.png)
![](/images/jenkins/20.png)

## 完美部署

## 暂时先写这么多吧，有空会继续更新...