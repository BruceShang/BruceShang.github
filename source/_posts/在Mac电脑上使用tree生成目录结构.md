---
title: 在Mac电脑上使用tree生成目录结构
date: 2018-07-20 14:35:28
tags: [tree, '目录结构']
categories: tree
---

## 生成markdown文件的目录结构，先上效果图

```js
.
├── build
│   ├── build.js
│   ├── check-versions.js
│   ├── logo.png
│   ├── utils.js
│   ├── vue-loader.conf.js
│   ├── webpack.base.conf.js
│   ├── webpack.dev.conf.js
│   └── webpack.prod.conf.js
├── config
│   ├── dev.env.js
│   ├── index.js
│   └── prod.env.js
├── index.html
├── package.json
├── src
│   ├── api
│   │   └── index.js
│   ├── assets
│   │   ├── images
│   │   └── stylus
│   ├── components
│   │   ├── checkbox
│   │   ├── index.js
│   │   ├── radio
│   │   └── topbar
│   ├── index.js
│   ├── main.js
│   ├── mixins
│   ├── router
│   │   ├── index.js
│   │   └── routes
│── └── views
│       ├── App.vue
│       ├── index
│       └── ui
├── static
│   └── .gitkeep
└── tree.md

```
<!-- more -->

### 1. Mac下使用brew install tree进行安装。安装之后在终端里，输入 tree -a 就可以查看当前目录下的缩影文件。

- 使用brew的时候，每次都会进行检查更新。每次更新特别慢，怎么把它给禁掉呢

```js
export HOMEBREW_NO_AUTO_UPDATE=true
```

- 在终端中执行这行代码后就能禁掉，现在试试就不再自动更新了。

### 2. tree的常用命令行参数

> -a 显示所有文件和目录。
> -A 使用ASNI绘图字符显示树状图而非以ASCII字符组合。
> -C 在文件和目录清单加上色彩，便于区分各种类型。
> -d 显示目录名称而非内容。
> -D 列出文件或目录的更改时间。
> -f 在每个文件或目录之前，显示完整的相对路径名称。
> -F 在执行文件，目录，Socket，符号连接，管道名称名称，各自加上"*","/","=","@","|"号。
> -g 列出文件或目录的所属群组名称，没有对应的名称时，则显示群组识别码。
> -i 不以阶梯状列出文件或目录名称。
> -I 不显示符合范本样式的文件或目录名称。
> -l 如遇到性质为符号连接的目录，直接列出该连接所指向的原始目录。
> -n 不在文件和目录清单加上色彩。
> -N 直接列出文件和目录名称，包括控制字符。
> -p 列出权限标示。
> -P 只显示符合范本样式的文件或目录名称。
> -q 用"?"号取代控制字符，列出文件和目录名称。
> -s 列出文件或目录大小。
> -t 用文件和目录的更改时间排序。
> -u 列出文件或目录的拥有者名称，没有对应的名称时，则显示用户识别码。
> -x 将范围局限在现行的文件系统中，若指定目录下的某些子目录，其存放于另一个文件系统上，则将该子目录予以排除在寻找范围外。

### 3. 我们根据自己的项目来实际操作一下

```js
tree -L 3 -I "node_modules" > tree.md
```
- 上面命令意思是显示项目下3层的所有文件结构，同时忽略node_modules文件夹，最后输出到tree.md