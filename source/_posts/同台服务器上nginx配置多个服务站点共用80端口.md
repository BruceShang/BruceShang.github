---
title: 同台服务器上nginx配置多个服务站点共用80端口
date: 2018-12-26 20:55:38
tags: ['Nginx']
categories: ['服务']
---

### 登录服务器修改主配置文件

```js
vim /etc/nginx/nginx.conf
```

```js
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    // 自定义的所有Nginx配置都自动加载到主配置文件中
    include /etc/nginx/conf.d/*.conf;
}
```
<!-- more -->
### 在 <code>/etc/nginx/conf.d</code>文件中创建一个自己的配置文件
> demp.conf 文件

```js
server {
    listen        8080; // 端口号
    server_name   test.com test2.com localhost; // 设置域名 可以写多个，与名之间用空格隔开

    location / {
        root   /root/xxx; // 资源文件地址
        index  index.html index.htm; // 指定入口文件
    }

    error_page  500 502 503 504 /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```
### 重启Nginx,发现报错了。

```js
Job for nginx.service failed because the control process exited with error code. See "systemctl status nginx.service" and "journalctl -xe" for details.、
```

##### 在网上查了一下资料，找到了问题的解决方案：

- 1.先查看一下日志<code>systemctl status nginx.service</code>

```js
nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
   Active: active (running) since 三 2018-12-26 19:47:13 CST; 1h 25min ago
  Process: 18294 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
  Process: 18290 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
  Process: 18289 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
 Main PID: 18296 (nginx)
   CGroup: /system.slice/nginx.service
           ├─18296 nginx: master process /usr/sbin/nginx
           └─18297 nginx: worker process

12月 26 19:47:13 instance-8c351jii systemd[1]: Starting The nginx HTTP and reverse proxy.....
12月 26 19:47:13 instance-8c351jii nginx[18290]: nginx: the configuration file /etc/ngin...ok
12月 26 19:47:13 instance-8c351jii nginx[18290]: nginx: configuration file /etc/nginx/ng...ul
12月 26 19:47:13 instance-8c351jii systemd[1]: Failed to read PID from file /run/nginx.p...nt
12月 26 19:47:13 instance-8c351jii systemd[1]: Started The nginx HTTP and reverse proxy ...r.
Hint: Some lines were ellipsized, use -l to show in full.
```

- 2.killall nginx
- 3.service nginx restart

### vue项目部署后，发现路由history模式刷新以后出现404，这是因为我们的是单页客户端应用

###### 第一种解决方案

```js
location /{
    root   /root/xxx;
    index  index.html index.htm;
    // 如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。
    if (!-e $request_filename) {
        rewrite ^/(.*) /index.html last;
        break;
    }
}
```

###### 第二种解决方案

```js
location / {
    root   /root/xxx;
    index  index.html index.htm;
    try_files $uri $uri/ /index.html; // 如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。
}
```
[官网给出的解决方案](https://router.vuejs.org/zh/guide/essentials/history-mode.html#%E5%90%8E%E7%AB%AF%E9%85%8D%E7%BD%AE%E4%BE%8B%E5%AD%90)

### 大功告成，问题都解决了。