---
title: mac环境下使用瓶子（Charles）抓包Https请求
tags: [Charles, mac Charles]
date: 2017-12-19 14:59:32
categories: Charles
---

##### Charles 根证书配置

1.选择 Charles 菜单“Help”->“SSL Proxying”->“Install Charles Root Certificate”
2.接着会自动弹出‘钥匙串访问’，默认情况下 Charles Proxy CA证书不会被信任的，右键选择显示简介将证书修改为始终信任

##### SSL Proxying Settings

选择菜单中“Proxy”->“SSL Proxying Settings…”添加使用通配符 Host Port ，表示抓取所有地址与端口的HTTPS请求
<!--more-->
##### 给手机安装证书

打开Charles，在Menu选择Help > SSL Proxying > Install Charles Root Certificate on a Mobile Device or Remote Browse
弹出一个弹框，根据提示使用手机访问地址： chls.pro/ssl 给手机安装证书

##### 最后别忘了在手机中将瓶子证书设置为信任，不然请求就会一直返回unknown

设置–>通用–>关于本机–>证书信任设置
把里面的那个Charles的证书设置为信任就可以了
